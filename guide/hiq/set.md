# set（变量）

Set的作用只为定义变量，在set中我们能对变量进行基础的运算，

## 变量的约定做法

变量的使用可以随意，使用时只需要注意符合yaml的规范能够被正确解析即可。但是为了规范和统一，我们一定也会有以下的约束

- 变量一律采用驼峰命名，一些固定变量命名方式保持一致， 如反连平台相关变量
    - `reverse: newReverse`
    - `reverseURL: reverse.url`
    - `reverseDomain: reverse.domain`
-  在变量中一定不要出现用户名密码等隐私内容(token，session等同理)；但是对于漏洞必须且非隐私的内容不作要求

!> 注意，对于目前的poc收取方案来说，我们并不会接收登陆后认证才能够利用的poc，如果想提交相关poc，需要考虑是否能使用特征匹配的方法进行检测，衡量误报漏报的风险后再次提交

## 使用中的常见问题

?> 下面小结的标题后会带有类型注释，表示在给出的类型中这些情况大多都会存在，需要我们在编写的过程中多加小心

### 未经处理的变量(int/string)

```yaml
set:
	n1: randomLowercase(4)
...
rules:
	r1:
		request:
			cache: true
			method: GET
			path: /xxx/yyy/{{n1}}.txt
			follow_redirects: false
		expression: response.status == 200 && response.body.bcontains(bytes(string(n1)))
	...
expression: r1()
```

对于一个变量，如果在一个rule内即作为请求的内容，有又作为匹配的内容，很有可能就会在一些无脑打印请求内容的站点中发生误报，这种问题是非常常见的。那么为了消除这个问题

- 可以将原变量处理后(md5/base64/....)后，再进行匹配
- 使用多个变量进行加/减/乘后，再进行匹配
- 不要直接在body中进行匹配，也许可以尝试在header等不常规的地方使用随机内容进行匹配

### 处理后过弱的变量(int)

```yaml
set:
	r1: randomInt(800000000, 1000000000)
	r2: randomInt(800000000, 1000000000)
rules:
	r0:
		request:
			cache: true
			method: GET
			path: /xxx&x509type=%27%0Aexpr%20{{r1}}%20-%20{{r2}}%0A%27
			follow_redirects: false
		expression: response.status == 200 && response.body.bcontains(bytes(string(r1 - r2)))
```

看上去没有问题，既使用了随机变量，又将变量相减后再进行判断。但是如果r1和r2范围相近，这条表达式的通用性就会急速上升，基本只要页面内存在这个小数字，就能够成功完成匹配

例如这种情况下，当r1恰好为600000000，r2恰好为599999999时，相减结果为1，那么这个时候只要页面中包含1这个字符的话就会直接导致误报的问题

那么对于这种情况，在两数字进行计算且计算之后未发生溢出时，我们做这样的要求

```text
( r1 - r2 ) => r1, r2 随机范围一定不同，且r1的随机范围要远小于r2
			=> 例如：r1: randomInt(1, 10), r2: randomInt(800000000, 1000000000)
( r1 + r2 ) => r1, r2随机范围不一定需要完全相同，但是需要注意随机数的起始范围
( r1 * r2 ) => r1, r2随机范围不一定需要完全相同，但是需要注意随机数的起始范围
```

同时`建议`增加匹配系统中特征内容，例如下面的内容

```yaml
name: poc-yaml-test-test
transport: http
set:
	r1: randomInt(1000, 3000)
	r2: randomInt(1000, 3000)
rules:
r0:
	request:
		method: POST
		path: /aaa.jsp
		headers:
		Content-Type: application/x-www-form-urlencoded
		body: cmd={{r1}}*{{r2}};
	expression: response.status == 200 && response.body_string.startsWith("<?xml") && response.body.bcontains(bytes(string(r1 * r2)))
expression: r0()
detail:
	author: y0y0
	links:
		- http://example.com
```

### 恰好相同的随机变量(int/string)

```yaml
set:
	r1: randomInt(800000000, 1000000000)
	r2: randomInt(800000000, 1000000000)
rules:
	rr0:
		request:
			method: POST
			path: /{{r1}}.txt
			header:
				Content-Type: multipart/form-data; boundary=----WebKitFormBoundary{{rBoundary}}
			body: >-
				------WebKitFormBoundary{{rBoundary}}
				Content-Disposition: form-data; name="myFile"; filename="test.jpg"
				
				<% out.println(\"{{r2}}\"); new java.io.File(application.getRealPath(request.getServletPath())).delete();%>
				------WebKitFormBoundary{{rBoundary}}--
		expression: true
	rr1:
		request:
			cache: true
			method: GET
			path: /{{r1}}.txt
			follow_redirects: false
		expression: response.status == 200 && response.body.bcontains(bytes(string(r2)))
expression: rr0() && rr1()
```

> 这种情况也许不多见，但是是发生过的例子

这条规则相比之前的那条似乎规则更强，发出的两个随机数在两个不同的请求中，没有交集的存在，很难发生误报。但是此时难得一见的事情发生了，r1和r2的随机值竟然相等，且在请求rr1时，会将请求的文件名称打印在页面中，那么此时恰好匹配到r2，发生了误报的现象

那么，对于这种情况，这里要求在使用同一个随机函数时，不同变量的随机范围一定要完全不同，相互之间不存在交集

### 变量间的引用

```yaml
set:
  s1: randomLowercase(10)
  base64s1: base64(s1)
  s2: base64s1 + "AAA"
```

在上述的例子中，我们定义了三个变量，其中 `s2` 的值是 `base64s1` 的值加上 `AAA`。由此可知，在set中，引用一个变量的值，不需要使用`{{}}`
将变量包裹起来，直接使用变量名称即可。从这个规则我们也可以引申出，在expression、output中，引用变量的值，也不需要使用`{{}}`将变量包裹起来，直接使用变量名称即可。

### 定义字符串的方式

在定义字符串的时候，有三种方式，如下所示：
```yaml
name: poc-yaml-test
transport: http
set:
  s1: string("hello world")
  s2: |
    "hello world"
  s3: '"hello world"'
rules:
  r0:
    request:
      method: POST
      path: /
      follow_redirects: false
      headers:
        Content-Type: text/xml
      body: |
        {{s1}} + {{s2}} + {{s3}}
    expression: response.status == 200
expression: r0()
detail:
  author: test
```
发出的请求如下：
```HTTP
POST / HTTP/1.1
Host: docs.xray.cool
Content-Length: 40
Content-Type: text/xml

hello world + hello world + hello world
```
可以发现三种形式都能够正常输出字符串，但是如果只使用单引号或者双引号作为开头，将会引起加载错误，比如：
```yaml
set:
  s1: "hello world"
  s2: 'hello world'
```
使用这样的写法就将加载失败。

## 三目运算符的使用方式

### 介绍

在许多编程语言中，三目运算符（也称为条件运算符）是一种简洁的表达条件逻辑的方式。在 CEL（Common Expression Language）中，三目运算符也是一种常用的表达式，其语法如下：
```yaml
条件表达式 ? 表达式1 : 表达式2
```
三目运算符的工作原理是，当条件表达式的结果为 true 时，整个表达式的值等于表达式1的值；当条件表达式的结果为 false 时，整个表达式的值等于表达式2的值。

例如，在 CEL 表达式中，你可以这样使用三目运算符：
```yaml
age >= 18 ? "Adult" : "Minor"
```
在这个例子中，如果 age 大于或等于 18，表达式的值将为 "Adult"，否则将为 "Minor"。这与以下的 if-else 语句相似：
```go
if age >= 18 {
    return "Adult"
} else {
    return "Minor"
}
```

### 案例

<!-- tabs:start -->

#### **mysql指纹匹配（截取）**

```yaml
name: fingerprint-yaml-tcp-mysql
manual: false
transport: tcp
set:
  GenericLines: b"\r\n\r\n"
payloads:
  payloads:
    l:
      re: '"(?s)^.\\0\\0\\0\\xffj\\x04''[\\d.]+'' .* MySQL"'
    m:
      re: '"(?s)^.\\0\\0\\0\\x0a(?P<version>5\\.[-_~.+:\\w]+MariaDB-[-_~.+:\\w]+~bionic)\\0"'
    n:
      re: '"(?s)^.\\0\\0\\0\\x0a(?P<version>[\\w._-]+)\\0............\\0\\x5f\\xd3\\x2d\\x02\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0............\\0$"'
    r:
      re: '"(?s)^.\\0\\0\\0\\x0a(?P<version>3\\.[-_~.+\\w]+)\\0...\\0"'
    s:
      re: '"(?s)^.\\0\\0\\0\\x0a(?P<version>4\\.[-_~.+\\w]+)\\0"'
rules:
  r1:
    request:
      cache: true
      content: '{{GenericLines}}'
    expression: re.bmatches(response.raw)
    output:
      result: re.bsubmatch(response.raw)
      osname: |
        re.contains("bionic") ? "Linux" : ""
      version: result["version"]
expression: r1()
detail:
  fingerprint:
    infos:
      - type: system_bin
        name: mysql
        version: '{{version}}'
      - type: operating_system
        name: '{{osname}}'
```

<!-- tabs:end -->
