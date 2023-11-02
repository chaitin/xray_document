# 全局变量载荷 - payload

payloads格式较为简单，一般来说能够通过lint检查就是一个正常的payloads，这里不做过多说明。但是对于该如何去使用payloads一直是一个头疼的问题，这里举一个简单的例子进行说明

对于某一个漏洞，我们有以下的虚拟情景

```text
某cve，能通过多个组件完成利用（这里举例为8个），同时不同组件能够使用一个统一的入口进行调用。
在调用组件之后，有一个统一的位置会存储着我们访问的结果，我们需要对产生的结果额外发出请求来匹配其中的内容，从而判断这个系统中是否存在相关的漏洞。
试考虑写出这个漏洞对应的yaml poc
```

## 优化之前，普通写法

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

## 优化之后，使用payloads进行编写

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
