# Redirect （URL重定向）

重定向漏洞发生时，说明从当前站点出发，下一个Location是我们所需要跳转的站点；跳转之后，所访问站点的Refer是先前的站点。一切都与SSRF有着不同之处

同时当有Redirect漏洞时，Location或者其他部分中的内容一定是经过系统处理后才进行输出的，具有一定的格式

这样来看，似乎大部分情况下我们只需要对response中的Location进行匹配即可，但是这里有几个点需要注意一下

## 关于follow_redirects

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

## 关于所需要跳转的站点

我们想要使用原理去验证Redirect漏洞的存在，就要去选择一个确切的站点去进行访问。这个内容一般情况下不固定，用户自由选择即可。唯一需要注意的一点就是一定需要选择一些`国内外均能够进行访问`，`返回内容简单`，`不牵扯隐私内容`的站点去进行访问

?> 对于这些内容，可以参考现有的poc模版相关内容，当然允许的情况下也可以是随机值

## 关于匹配的内容

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

- 当返回内容的特征中的内容非常固定时，`可以`使用`==`进行去进行匹配，这种情况下对格式的要求更为严苛

```yaml
r0:
	request:
		cache: true
		method: GET
		path: /new/newhttp:/interact.sh?
		follow_redirects: false
	expression: response.status == 302 && response.headers["location"] == "http:/interact.sh?" && response.body.bcontains(b"http:/interact.sh?\">Found</a>.")
```

### 为什么不能使用contains去匹配

contains表明了匹配的内容中只要包含了我们所给出的特征即可，并没有格式的要求。那么这就会导致误报的问题，这里举出一个例子进行分析，这个例子是之前审核中所遇到过的一个情景

```text
一个网站并没有Redirect漏洞，但是当我们访问发出的path时，如果系统里并没有对于这条path的路由，也会发生302跳转，同时把访问地址的全部内容（schema+uri+path）全部塞入Location中
```

那么当跳转后，由于Location也仅仅只是当前站点的地址，并没有访问到我们所期望的站点。但是这时由于我们使用了contains，且Location中也有相关内容，那么就会导致误报的问题

所以综上，在redirect漏洞的探测中，我们一定不要去使用contains去进行匹配

## 模板

[Redirect](/guide/yaml/yaml_poc_template?id=ssrfurl跳转类)
