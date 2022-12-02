# 一个基础的POC的构成

### 相关视频教程：
[一个基础POC的构成](https://www.bilibili.com/video/BV1Lr4y1L7HC)

### 最基础的 POC 

```yaml
name: poc-yaml-example-com
# 脚本部分
transport: http
rules:
    r1:
        request:
            method: GET
            path: "/"
        expression: |
            response.status==200 && response.body.bcontains(b'Example Domain')
expression:
    r1()
# 信息部分
detail:
    author: name(link)
    links: 
        - http://example.com
```

整个 POC 大致可以分为 3 部分：

- 名称： 脚本名称, string 类型
- 脚本部分：主要逻辑控制部分，控制着脚本的运行过程
- 信息部分：主要是用来声明该脚本的一些信息，包括输出内容

### POC的检测逻辑与关键词

#### POC检测执行逻辑

为了帮助大家更好的理解 poc 中各部分的作用，此处先介绍一下一个 yaml poc 的执行过程。

在一个 yaml poc 从文件加载到 go 的某个结构后，会首先对表达式进行预编译和静态类型检查，这一过程主要作用于 yaml 中的 set 和 expression 部分，这两部分是 yaml poc 的关键，主要用到了 CEL 表达式。

在检查完成后，内存中的 poc 就处于等待调度的状态了。每当有**新的请求**来临时，会执行类似如下的伪代码:

```go
for rule in rules:
    newReq = mutate_request_by_rule(req, rule)
    response = send(newReq)
    if not check_response(response, rule):
        break
```

简单来讲就是将请求根据 rule 中的规则对请求变形，然后获取变形后的响应，再检查响应是否匹配 `expression` 部分的表达式。如果匹配，就进行下一个 rule，如果不匹配则退出执行。 如果成功执行完了最后一个 rule，那么代表目标有漏洞，将 detail 中的信息附加到漏洞输出后就完成了单个 poc 的整个流程。

目前版本的实现对**新的请求**定义为: **新的目录**, 比如下面几个 url 依次进入检查队列时，执行情况如下：

```
http://exmaple.com/     会执行, 上下文为 / 
http://example.com/a    不会再次执行，因为上下文同样为 /
http://example.com/a/b  会执行，上下文为 /a/
http://example.com/a/c/ 不会执行, 超过深度限制 (depth)
```

其中 `depth` 是 phantasm 插件的一个配置项，用于指定检测深度

#### poc主体的关键字的意义

```yaml
rules:
    r0:
        request:
            cache: true
            method: POST
            path: "/"
            headers:
                Content-Type: application/json
            follow_redirects: true
            body: '{"username":"administrator","password":"1qazxsw23edcvfr4"}'
        expression: |
            response.status==200 && "welcome, administrator, xxxxxxxxxx".bmatches(response.body)
    r1:
        request:
            cache: true
            method: GET
            path: "/fetchBody?id=1/../../../../../../../../etc/passwd"
        expression: |
            response.status == 200 && "root:[x*]:0:0:".bmatches(response.body)
expression: r0() && r1()
```

1.  rules以及单个rule的名称 
   - **rules**代表着一个规则集，在这个规则集中，将存放着所有要发送的信息以及要判断的规则
   - **rule**则是一个请求的规则，代表你想要发送什么样的请求。如上述所举的例子中，r0,r1是规则的名称
2.  request
该关键词中存在着构建一个请求包所要填写的信息，包括请求使用的方法，请求路径，请求头，请求body，是否跟随302跳转。 
   - `cache: bool` 是否使用缓存的请求，如果该选项为 true，那么如果在一次探测中其它脚本对相同目标发送过相同请求，那么便使用之前缓存的响应，而不发新的数据包
   - `method: string` 请求方法
   - `path: string` 请求的完整 Path，包括 querystring 等 (详情见： [HTTP PATH 的使用](https://docs.xray.cool/#/guide/skill/path)) 
      1. 如果 path 是以 `/` 开头的， 取 dir 路径拼接
      2. 如果 path 是以 `^` 开头的， uri 直接取该路径
   - `headers: map[string]string` 请求 HTTP 头，Rule 中指定的值会被覆盖到原始数据包的 HTTP 头中
   - `body: string` 请求的Body（转译问题见：[头疼的转译](https://docs.xray.cool/#/guide/skill/escape)）
   - `follow_redirects: bool` 是否允许跟随300跳转, 默认为true
3.  expression
在rule下的`expression`是用来对返回包（response）进行匹配的，你可以编写各种各样的限制来判断返回包中信息，从而确认返回的内容是否符合要求。
正如spring使用SpEL表达式，struts2使用OGNL表达式，xray使用了编译性语言Golang，所以为了实现动态执行一些规则，我们使用了Common Expression Language (CEL)表达式： 
```
response.status==200 && response.body.bcontains(b'Example Domain')
```

CEL表达式通熟易懂，非常类似于一个Python表达式。上述表达式的意思是：**返回包的status等于200，且body中包含内容“Example Domain”**。
expression表达式上下文还包含有一些常用的函数。比如上述 `bcontains` 用来匹配 bytes 是否包含，类似的，如果要匹配 string 的包含，可以使用 `contains`, 如： 
值得注意的是，类似于python，CEL中的字符串可以有转义和前缀，如：(详情见：[头疼的转义](https://docs.xray.cool/#/guide/skill/escape)) 

   - `'\r\n'` 表示换行
   - `r'\r\n'` 不表示换行，仅仅表示这4个字符。在编写正则时很有意义。
   - `b'test'` 一个字节流（bytes），在golang中即为`[]byte`

用一些简单的例子来解释大部分我们可能用到的表达式： 

   - `response.body.bcontains(b'test')` 
      - 返回包 body 包含 test，因为 body 是一个 bytes 类型的变量，所以我们需要使用 bcontains 方法，且其参数也是 bytes
   - `response.body.bcontains(bytes(r1+'some value'+r2))` 
      - r1、r2是 randomLowercase 的变量，这里动态的判断 body 的内容
   - `response.content_type.contains('application/octet-stream') && response.body.bcontains(b'\x00\x01\x02')` 
      - 返回包的 content-type 包含 application/octet-stream，且 body 中包含 0x000102 这段二进制串
   - `response.content_type.contains('zip') && r'^PK\x03\x04'.bmatches(response.body)` 
      - 这个规则用来判断返回的内容是否是zip文件，需要同时满足条件：content-type 包含关键字 "zip"，且 body 匹配上正则r'^PK\x03\x04'（就是zip的文件头）。因为 startsWith 方法只支持字符串的判断，所以这里没有使用。
   - `response.status >= 300 && response.status < 400` 
      - 返回包的 status code 在 300~400 之间
   - `(response.status >= 500 && response.status != 502) || r'<input value="(.+?)"'.bmatches(response.body)` 
      - 返回包status code大于等于500且不等于502，或者Body包含表单
   - `response.headers['location']=="https://www.example.com"` 
      - headers 中 `Location` 等于指定值，如果 `Location` 不存在，该表达式返回 false
   - `'docker-distribution-api-version' in response.headers && response.headers['docker-distribution-api-version'].contains('registry/2.0')` 
      - headers 中包含 `docker-distribution-api-version` 并且 value 包含指定字符串，如果不判断 `in`，后续的 contains 会出错。
   - `response.body.bcontains(bytes(response.url.path))` 
      - body 中包含 url 的 path

每一个expression表达式都会返回一个bool结果，然后在存在&&或||的时候，与其他的expression表达式做运算，最终出一个结果，最终结果如果为true，则代表这个规则命中。

#### set关键字的使用

该字段主要是用来定义一些在接下来的规则中会使用到的一些全局变量，比如随机数，反连平台等。 `set: map[string]interface{}`

```yaml
set:
    a: 1
    num: randonInt(1000, 2000)      # 1543
    rstr: randomLowercase(10)       # asdbeuyekp
    reverse: newReverse()
    reverseURL: reverse.url
```

#### payload关键词的使用

该字段用于定义多个 payload，来实现发送不同 payload 的效果。 该字段结构如下

| 变量名/函数名 | 类型 | 说明 |
| --- | --- | --- |
| `continue` | `bool` | 命中一个之后是否继续 |
| `payloads` | `map[string]Set` | 和 `set`
 一样的结构和语法 |


形如：

```yaml
payloads:
  continue: false
  payloads:
    ping:
      cmd: r"ping test.com"
    curl:
      cmd: r"curl test.com"
```

**注**:

1. 只支持罗列 `payload`， 目前不考虑支持文件或者复杂排列组合等情况，每个 `payload` 中的 `key` 必须严格一致
2. 循环 payload 然后把当前 payload 加在 set 之后，组成新的 set 执行

```yaml
set:
    a: 1
payloads:
    - b: a
    - b: b
```

实际运行结果相当于，运行两遍

第一遍：

```yaml
set:
    a: 1
    b: a
```

第二遍：

```yaml
set:
    a: 1
    b: b
```

#### output与search的组合使用

1. 获取返回的token
2. 获取上传文件后返回的文件路径
3. 总结：获取所需要的参数

返回包

```http
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8;

{"pbx":"COMpact 4000","pbxType":20,"pbxId":0,"serial":"4107646840","date":"05.07.2022","macaddr":"00:01:01:01:01:01"}
```

匹配

```yaml
rules:
    r1:
        request:
            method: GET
            path: "/about_state"
        expression: response.status == 200 && r'serial.*?\d+.,'.bmatches(response.body) && r'date.+?20[0-9][0-9].,'.bmatches(response.body)
        output:
            search: '"serial\":\"(?P<serial>.+?)\",\"date".bsubmatch(response.body)'
            serial: search["serial"]        // 4107646840
            search1: '"date\":\"(?P<date>.+?)\",\"macaddr".bsubmatch(response.body)'            
            date: search1["date"]           // 05.07.2022
            HA: serial + "r2d2" + date      // 4107646840r2d205.07.2022
            password: substr(md5(HA),0,7)   // e3048ad
```

#### detail的编写

该字段用于定义一些和脚本相关的信息。

```yaml
detail:
    author: name(link)
    links: 
        - http://example.com
```

其中author后面跟的link可以填写自己的博客，github主页等信息，也可以不填写。
而links则是填写这个poc的来源地址，或者这个漏洞的相关描述等，方便审核人员审核，也方便扫描出该漏洞的师傅去复现，修复漏洞。
所以在审核的时候，如果链接出现问题，我们也将不会通过。
