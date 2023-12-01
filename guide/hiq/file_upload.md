# file upload（文件上传）

## 普通情况

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
			body: "\
				------WebKitFormBoundary{{rBoundary}}\r\n\
				Content-Disposition: form-data; name=\"myFile\"; filename=\"test.jpg\"\r\n\
				\r\n\
				<% out.println(\"{{s1}}\"); new java.io.File(application.getRealPath(request.getServletPath())).delete();%>\r\n\
				------WebKitFormBoundary{{rBoundary}}--\r\n\
				"
			headers:
				Content-Type: multipart/form-data; boundary=----WebKitFormBoundary{{rboundary}}
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
        - 如果`上传的文件名称由系统产生`，且`生成的文件就是系统本身的文件`，即产生了`文件覆盖`的现象，那么`无论如何`，我们都`不要尝试`去上传对应的文件，那么这个时候我们就需要找到`另外的方法`去验证漏洞的存在与否
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

## 特殊情况

上面这些说明了常见情况下的上传脚本文件的验证方式，如果依据上述内容排查下来，仍然不符合需求的话，这里提出几个常见的问题：

- 对于文件上传漏洞，如果实在难以找到无害化上传的方法，应该如何去进行判断poc是否可行？
    - 对于这个问题，如果仍然希望写出无害化的poc，那么我们可以抛去原理验证的方式，尝试去找一些`一定`只有存在漏洞的版本才能出现的信息，通过匹配这些内容，来侧方位地验证漏洞的存在

> 但是寻常情况下比较不推荐使用该方法进行验证，

- 如果上传的文件内容是二进制文件，比如zip，暂时没有办法实现上述所说的随机化内容，应该如何处理？
    - 这种情况下现阶段的情况下不是很推荐再去编写`yaml poc`进行验证，可以等待使用`go poc`完成随机化的处理

- **如果是使用系统命令创建的文件，首先尽量不要创建脚本类型的文件，尽量是一个txt文件；其次，不要再多发一个包进行文件的删除，因为这一般就会使用rm命令，这个命令在目标系统上执行是非常危险的行为，所以宁愿留一个文件，也不要执行相关的命令**

## 模板

[文件上传](/guide/yaml/yaml_poc_template?id=文件上传)
