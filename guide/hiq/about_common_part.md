# 脚本通用部分

这部分主要会对yaml poc的大体格式进行规范，同时对其中的一些细节内容讲解一下审核中的规范

## Set（变量）

Set的作用只为定义变量，在set中我们能对变量进行基础的运算，

### 变量的约定做法

变量的使用可以随意，使用时只需要注意符合yaml的规范能够被正确解析即可。但是为了规范和统一，我们一定也会有以下的约束

- 变量一律采用驼峰命名，一些固定变量命名方式保持一致， 如反连平台相关变量
	- `reverse: newReverse`
	- `reverseURL: reverse.url`
	- `reverseDomain: reverse.domain`
-  在变量中一定不要出现用户名密码等隐私内容(token，session等同理)；但是对于漏洞必须且非隐私的内容不作要求

!> 注意，对于目前的poc收取方案来说，我们并不会接收登陆后认证才能够利用的poc，如果想提交相关poc，需要考虑是否能使用特征匹配的方法进行检测，衡量误报漏报的风险后再次提交

### 使用中的常见问题

?> 下面小结的标题后会带有类型注释，表示在给出的类型中这些情况大多都会存在，需要我们在编写的过程中多加小心

#### 未经处理的变量(int/string)

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

#### 处理后过弱的变量(int)

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

#### 恰好相同的随机变量(int/string)

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

## payloads（optional）

payloads格式较为简单，一般来说能够通过lint检查就是一个正常的payloads，这里不做过多说明。但是对于该如何去使用payloads一直是一个头疼的问题，这里举一个简单的例子进行说明

对于某一个漏洞，我们有以下的虚拟情景

```text
某cve，能通过多个组件完成利用（这里举例为8个），同时不同组件能够使用一个统一的入口进行调用。在调用组件之后，有一个统一的位置会存储着我们访问的结果，我们需要对产生的结果额外发出请求来匹配其中的内容，从而判断这个系统中是否存在相关的漏洞。试考虑写出这个漏洞对应的yaml poc
```

### 优化之前，普通写法

那么结合这个场景，我们可以有以下的思路

```text
* 不同组件，同一个入口         -> 可以编写多个rule，在path填写不同的组件参数，同时在最终的表达式中使用`或`进行分隔
* 不同组件的返回结果或许不太相同 -> 在表达式的匹配中，使用正则或者其他匹配语句对不同的特性进行匹配
* 每次利用后需要匹配一些额外信息 -> 这个需要搭配第一条使用，在每一个这种rule后再加上其他的辅助rule进行匹配
```

那么结合这样的思路，我们可以得出以下的内容

```yaml
name: poc-yaml-test-payloads
manual: true
transport: http
rules:
  r0:
    request:
      method: POST
      path: /admin%20/upload-srv-1
      headers:
        Content-Type: application/x-www-form-urlencoded
      body: xxxxxxxxxxxx
    expression: |
      response.status == 200 && response.body_string.contains("aaaavvvcccc")
  v0:
    request:
      method: GET
      path: /aaaavvvcccc
    expression: |
      "aaaaaaaa".bmatches(response.body)
  r1:
    request:
      method: POST
      path: /admin%20/upload-srv-2
      headers:
        Content-Type: application/x-www-form-urlencoded
      body: xxxxxxxxxxxx
    expression: |
      response.status == 200 && response.body_string.contains("aaaavvvcccc")
  v1:
    request:
      method: GET
      path: /aaaavvvcccc
    expression: |
      "bbbbbbbb".bmatches(response.body)
  r2:
    request:
      method: POST
      path: /admin%20/upload-srv-3
      headers:
        Content-Type: application/x-www-form-urlencoded
      body: xxxxxxxxxxxx
    expression: |
      response.status == 200 && response.body_string.contains("aaaavvvcccc")
  v2:
    request:
      method: GET
      path: /aaaavvvcccc
    expression: |
      "cccccccc".bmatches(response.body)
  r3:
    request:
      method: POST
      path: /admin%20/upload-srv-4
      headers:
        Content-Type: application/x-www-form-urlencoded
      body: xxxxxxxxxxxx
    expression: |
      response.status == 200 && response.body_string.contains("aaaavvvcccc")
  v4:
    request:
      method: GET
      path: /aaaavvvcccc
    expression: |
      "dddddddd".bmatches(response.body)
expression: r0() && v0() || r1() && v1() || r2() && v2() || r3() && v3()
detail:
  author: Chaitin
  links:
    - http://example.com
```

### 优化之后，使用payload进行编写

那么考虑一下上边写出的内容，我们发现貌似不同rule之间是有一定规律的，而且poc总体看上去过为冗长，不利于阅读。这里给出考虑到的问题

```text
* 不同组件，同一个入口         -> 既然入口相同，只是后边的参数或者部分路径不同，或许可以提取出不同的部分，归纳相同的部分，将这些内容分离
* 不同组件的返回结果或许不太相同 -> 虽然返回的内容不大相同，但是貌似对于返回的结果都有相似的匹配方式，那么将这些内容分离单独写出
* 每次利用后需要匹配一些额外信息 -> 考虑到第一条的抽象理解，貌似也不需要每个rule都要额外写一遍，只需要直接写出，让这些额外的内容自动去结合能够进行抽象理解的rule即可
```

那么结合下这样的思路，再考虑到已经给出的payloads字段，我们可以获得这样的内容
```yaml
name: poc-yaml-test-payloads
manual: true
transport: http
payloads:
  payloads:
    upload1:
      path: |
        "upload-srv-1"
      body: |
        "xxxxxxxxxxxx"
      re: |
        "aaaaaaaa"
    upload2:
      path: |
        "upload-srv-2"
      body: |
        "xxxxxxxxxxxx"
      re: |
        "bbbbbbbb"
    upload3:
      path: |
        "upload-srv-3"
      body: |
        "xxxxxxxxxxxx"
      re: |
        "cccccccc"
    upload4:
      path: |
        "upload-srv-4"
      body: |
        "xxxxxxxxxxxx"
      re: |
        "dddddddd"
rules:
  r0:
    request:
      method: POST
      path: /admin%20/{{path}}
      headers:
        Content-Type: application/x-www-form-urlencoded
      body: |
        "{{body}}"
    expression: |
      response.status == 200 && response.body_string.contains("aaaavvvcccc")
  verify:
    request:
      method: GET
      path: /aaaavvvcccc
    expression: |
      re.bmatches(response.body)
expression: r0() && verify()
detail:
   author: Chaitin
   links:
    - http://example.com
```

按照payload的展开规则展开后两者的expression其实是相同的，但是将两者进行对比，我们可以发现，优化之后的poc相比优化之前的poc，内容上大概有以下方面的强化

- 天然抽象出了漏洞的利用路径，忽略了一些暂时无需多加关注的信息，能让我们更加关注于如何通过这样一条路径去分辨漏洞的存在与否
- payload的`名称`和内部具体需要填充的内容都可以以key的形式进行呈现，非常有利于我们对不同的利用方式进行区分与修改，同时不必去费尽心思去想如何对不同的rule进行命名
- 大幅缩短了所编写poc的长度，在方便日常阅读的同时更有利于后续整体的大小优化
- 在需要添加一些利用手段时，只需要在payload中继续添加利用方式即可，而不用再回到rule中，重新了解rule该去如何编写

综上，当我们执行的rule数量较多且互相重复(拥有相同的访问路径，参数以及判断表达式)时，`非常推荐`将rule抽象为payloads进行处理

## rules（漏洞规则部分）

这部分的内容与下一个章节有所重合，那么对于漏洞的相关内容这里不多加赘述，这里只对rule中出现的一些特性进行说明和分析

### Http Path的使用

web服务的访问路径会因为对应的安装路径以及系统特性而有所不同。在编写poc的过程中时，如果想要写出更为通用的poc，那么就要对web系统的访问路径有更多的了解，了解它是固定路径还是动态路径

在yaml poc中，为了应对更多的网络环境，这里提供了两种拼接Http Path的方法。在path中
- 如果以 / 开头，取的传入 url 的 dir path 拼接的
	- 也就是说会动态地获取当前的访问路径，再对我们传入的path内容进行拼接
- 如果以^开头，取 path 作为请求路径
	- 也就是说只会获取URI的部分，之后直接将传入的path拼接到URI的后面

这里给出具体的例子进行参考

```yaml
# target: http://example.com:8080/test/test
rules: 
	r0: 
		request: 
			cache: true 
			method: GET 
			# target: http://example.com:8080/test/a 
			path: /a 
		expression: "true" 
		output: 
			r0Url: request.url.path 
	r1: 
		request: 
			cache: true 
			method: GET 
			# target: http://example.com:8080/test/test/b 
			path: '^{{inputPath}}/b' 
		expression: "true" 
	r2: 
		request: 
			cache: true 
			method: GET 
			# target: http://example.com:8080/c 
			path: ^/c 
		expression: "true" 
	r3: 
		request: 
			cache: true 
			method: GET 
			# target: http://example.com:8080/test/a/d
			path: '^{{r0Url}}/d' 
		expression: "true"
```

所以无论是在日常的审核中或者是poc的编写中，我们都要全面分析当前使用的方式是不是能满足覆盖绝大多数中的情况

### Header的使用

header中的内容并没有过多要求，但是对于现阶段的yaml poc，我们仍然会有以下的限制

- 对于Content-Type，Accept等系统内部已经提供的header，如果并不是目标所特需的，请一定不要填写这些内容

### Output的使用

当我们使用的ouput时，就表示当前所访问的页面内一定有我们所需要访问的内容。我们把这些内容提取出来并且作为变量来供给其他rule来进行使用

那么反过来考虑，当页面内有我们想要的内容时，我们能不能把它作为一个expression来进行匹配呢？答案是肯定的

不知如此，这里我们做出`规定`，当使用output进行内容匹配时，一定要把search中的内容作为一个匹配内容写在对应rule的expression中，尽可能地作为指纹来降低误报率以及减少发包量。这里给出例子进行解释

```yaml
name: poc-yaml-wanhuoa-upload-rce
manual: true
transport: http
set:
	rfilename: randomLowercase(4)
	r2: randomInt(40000, 44800)
	r3: randomInt(40000, 44800)
rules:
	r11:
		request:
			cache: true
			method: POST
			path: /xxx/controller
			headers:
			Content-Type: multipart/form-data; boundary=b0d829daa06c13d6b3e16b0ad21d1eed
			body: "<file content>"
		expression: response.status == 200 && response.body.bcontains(b"success") && "\"data\":\\d+\\.jsp".bmatches(response.body)
		output:
			search: '"data\":\"(?P<filename>\\d+).jsp".bsubmatch(response.body)'
			uploadfilename: search["filename"]
	r22:
		request:
			cache: true
			method: GET
			path: /aaa/{{uploadfilename}}.jsp
		expression: response.status == 200 && response.body.bcontains(bytes(string(r2 * r3)))
expression: r11() && r22()
detail:
	author: chaitin
	links:
		- http://example.com
```

?> 同时在编写search相关内容时，一定要注意引号的转义问题，保证整体的正常运行

## detail（信息部分）

这部分内容的填写也是我们需要具体关注的。虽然对于漏洞的识别来说它并没有任何的作用，但是一个规范良好的detail是方便双方的存在

detail在代码中的体现是一个结构体，并不是一个Map。一般来说，detail本身必须被声明，且内部需要我们进行填写的只有`author`与`links`两项

### 关于author

author就是作者的名称，一般来说这个内容是根据我们个人的意愿进行填写的，当私人使用时可以填写任何内容。但是当提交到平台且被收录后公用的时候，这个内容是会展示到我们的漏洞报告中去的(命令行界面中红色输出部分就有这些内容)，为了统一规范与避免推广滥用，我们要求

- author中的名称必须是编写者本人的昵称，需要与提交站点上提交人相对应
- 如果想补充个人链接，名称后可以使用`()`包裹个人的github等链接内容，不建议填入本人的博客地址等内容

### 关于links （optional）

links表明了该漏洞相关的信息，通过查阅这些链接中给出的信息后，我们能够对漏洞的利用手段以及触发条件有一个大致的了解

!> 在一些漏洞的编写过程中，由于时间或保密性等原因，这些漏洞的细节内容并没有被公开，基本处于0信息的状态。那么对于这种类型的漏洞我们`并不强求`填写links内容，可以选择留空而不是填写一些无关紧要的内容，例如产品的官网等链接

那么在能填写links的poc中，我们一般有以下的要求

- 链接需要是公共的，能够长时间进行访问的，不可以使用私人站点的链接进行填写
- `不建议`使用个人的一些博客链接进行填写，一方面考虑到个人隐私相关问题，另一方面一些个人博客的链接也是需要访问权限的
- `不建议`直接使用漏洞的详情页当作links进行填写，例如cve.mitre.org，cnvd.org.cn等

不强求一定需要填写过多的链接，只需要填写一些常见且表明了漏洞具体出处和利用手法的链接即可
