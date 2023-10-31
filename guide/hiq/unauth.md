# unauth/idor（未授权访问/越权）

这个类型的poc所匹配的内容非常不固定，这里给出在验证中大致需要注意的问题

- 所访问的path一定是对应漏洞的path
- 所匹配的页面内容尽可能地突出当发生未授权访问或者越权时，这个页面中出现了什么特殊的内容
- 如果上述匹配的特征过少或者规则稍弱，建议增加指纹等的验证规则

这里举出审核中遇见的例子进行讲解

## H3C Unauth

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

## 模板

- [未授权类](/guide/yaml/yaml_poc_template?id=未授权类)
