# 漏洞规则部分 - rules

这部分的内容与下一个章节有所重合，那么对于漏洞的相关内容这里不多加赘述，这里只对rule中出现的一些特性进行说明和分析

## Http Path的使用

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

## Header的使用

header中的内容并没有过多要求，但是对于现阶段的yaml poc，我们仍然会有以下的限制

- 对于Content-Type，Accept等系统内部已经提供的header，如果并不是目标所特需的，请一定不要填写这些内容

## Output的使用

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
