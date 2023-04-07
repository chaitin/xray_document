# 一个基础的YAML插件的构成

## 相关视频教程：
[一个基础YAML插件的构成](https://www.bilibili.com/video/BV1Lr4y1L7HC)

## 最基础的 YAML 插件

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
            response.status==200 && response.body_string.contains("Example Domain")
expression:
    r1()
# 信息部分
detail:
    author: name(link)
    links: 
        - http://example.com
```

整个 YAML 插件大致可以分为 3 部分：

- 名称： 脚本名称, string 类型
- 脚本部分：主要逻辑控制部分，控制着脚本的运行过程
- 信息部分：主要是用来声明该脚本的一些信息，包括输出内容

## YAML插件的脚本部分

### 传输方式（transport）

该字段用于指定发送数据包的协议。 `transport： string`

形如：

```yaml
transport: http
```

目前 transport 的取值可以为以下 3 种之一：

1. tcp
2. udp
3. http

相比于 v1 版本，v2 版本做出的一个重要变更是允许发送多种多样的数据包了，不再仅局限于 http 请求，我们新增 tcp 和 udp 的支持。

目前不允许一个脚本中发送不同种 transport 的请求，因为通常我们的输入是一个稳定的协议， 比如：

1. 端口存活探测的结果通常会知道它是 tcp 或者 udp 存活
2. 或者从一个明确的 http 请求或者 web 站点开始

如果后续有其它协议的需求，这个字段的取值是可以扩展的。

### YAML插件主体的关键字的意义

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
            response.status==200 && "welcome, administrator, xxxxxxxxxx".matches(response.body_string)
    r1:
        request:
            cache: true
            method: GET
            path: "/fetchBody?id=1/../../../../../../../../etc/passwd"
        expression: |
            response.status == 200 && "root:[x*]:0:0:".matches(response.body_string)
expression: r0() && r1()
```

1. rules以及单个rule的名称 
   - **rules**代表着一个规则集，在这个规则集中，将存放着所有要发送的信息以及要判断的规则
   - **rule**则是一个请求的规则，代表你想要发送什么样的请求。如上述所举的例子中，r0,r1是规则的名称

2. request

    该关键词中存在着构建一个请求包所要填写的信息，包括请求使用的方法，请求路径，请求头，请求body，是否跟随302跳转。 
    - `cache: bool` 是否使用缓存的请求，如果该选项为 true，那么如果在一次探测中其它脚本对相同目标发送过相同请求，那么便使用之前缓存的响应，而不发新的数据包
    - `method: string` 请求方法
    - `path: string` 请求的完整 Path，包括 querystring 等 (详情见： [HTTP PATH 的使用](https://docs.xray.cool/#/guide/skill/path)) 
      1. 如果 path 是以 `/` 开头的， 取 dir 路径拼接
      2. 如果 path 是以 `^` 开头的， uri 直接取该路径
    - `headers: map[string]string` 请求 HTTP 头，Rule 中指定的值会被覆盖到原始数据包的 HTTP 头中
    - `body: string` 请求的Body（转译问题见：[头疼的转译](https://docs.xray.cool/#/guide/skill/escape)）
    - `follow_redirects: bool` 是否允许跟随300跳转, 默认为true

3. expression

    在rule下的`expression`是用来对返回包（response）进行匹配的，你可以编写各种各样的限制来判断返回包中信息，从而确认返回的内容是否符合要求。
    正如spring使用SpEL表达式，struts2使用OGNL表达式，xray使用了编译性语言Golang，所以为了实现动态执行一些规则，我们使用了Common Expression Language (CEL)表达式： 
    ```
    response.status==200 && response.body_string.contains("Example Domain")
    ```

    CEL表达式通熟易懂，非常类似于一个Python表达式。上述表达式的意思是：**返回包的status等于200，且body中包含内容“Example Domain”**。
    expression表达式上下文还包含有一些常用的函数。比如上述 `bcontains` 用来匹配 bytes 是否包含，类似的，如果要匹配 string 的包含，可以使用 `contains`, 如： 
    值得注意的是，类似于python，CEL中的字符串可以有转义和前缀，如：(详情见：[头疼的转义](https://docs.xray.cool/#/guide/skill/escape)) 

   - `'\r\n'` 表示换行
   - `r'\r\n'` 不表示换行，仅仅表示这4个字符。在编写正则时很有意义。
   - `b'test'` 一个字节流（bytes），在golang中即为`[]byte`

    用一些简单的例子来解释大部分我们可能用到的表达式： 

     - `response.body_string.contains("test")` 
      - 返回包 body 包含 test，因为 body 是一个 bytes 类型的变量，所以我们需要使用 bcontains 方法，且其参数也是 bytes
     - `response.body_string.contains(r1 + "some value" + r2)` 
        - r1、r2是 randomLowercase 的变量，这里动态的判断 body 的内容
     - `response.content_type.contains('application/octet-stream') && response.body.bcontains(b'\x00\x01\x02')` 
        - 返回包的 content-type 包含 application/octet-stream，且 body 中包含 0x000102 这段二进制串
     - `response.content_type.contains('zip') && r'^PK\x03\x04'.bmatches(response.body)` 
        - 这个规则用来判断返回的内容是否是zip文件，需要同时满足条件：content-type 包含关键字 "zip"，且 body 匹配上正则r'^PK\x03\x04'（就是zip的文件头）。因为 startsWith 方法只支持字符串的判断，所以这里没有使用。
     - `response.status >= 300 && response.status < 400` 
        - 返回包的 status code 在 300~400 之间
     - `(response.status >= 500 && response.status != 502) || "<input value=\"(.+?)\"".matches(response.body_string)` 
        - 返回包status code大于等于500且不等于502，或者Body包含表单
     - `response.headers['location']=="https://www.example.com"` 
        - headers 中 `Location` 等于指定值，如果 `Location` 不存在，该表达式返回 false
     - `'docker-distribution-api-version' in response.headers && response.headers['docker-distribution-api-version'].contains('registry/2.0')` 
        - headers 中包含 `docker-distribution-api-version` 并且 value 包含指定字符串，如果不判断 `in`，后续的 contains 会出错。
     - `response.body_string.contains(response.url.path)` 
        - body 中包含 url 的 path

    每一个expression表达式都会返回一个bool结果，然后在存在&&或||的时候，与其他的expression表达式做运算，最终出一个结果，最终结果如果为true，则代表这个规则命中。
### 与rules同级的expression的使用

对于脚本层级的 expression，这个结果作为最后脚本是否匹配成功的值，通常脚本层级的 expression 是 rule 结果的一个组合。 比如一个脚本包含 `r1`, `r2`, `r3`，`r4` 4 条规则， 作为脚本层级的 expression，其全局变量将会定义  `r1`, `r2`, `r3`， `r4`  4 个函数，调用这个 4 个函数即可获得它对应 rule 的结果。

```yaml
expression: r1() && r2() && r3() && r4()
```

**注**：

相比于 V1 版本， rule 如何运行这件事情发生了较大的改变。假设我们有 `r1`, `r2`, `r3`，`r4` 4 条规则

最开始时， V1 版本添加了 `rules: []Rule` 来定义 rule 及其执行顺序。其逻辑为顺序执行 rule， 且 `r1`, `r2`, `r3`，`r4` 都为 true 时， 脚本执行成功

```yaml
rules: 
    r1: ...
    r2: ...
    r3: ...
    r4: ...
```

然后添加了 `group: map[string][]Rule` 拓展了一下 ruel 的执行方式。支持了 r1，r2 同时为 true 或者 r3, r4 同时为 true 时， 脚本执行成功

```yaml
group:
    g1: [r1, r2]
    g2: [r3, r4]
```

在 V2 版本下， 我们使用 expression 来组织 rule 的执行逻辑。

对于方式1，其对应的形式为：

```yaml
rules: 
    r1: ...
    r2: ...
    r3: ...
    r4: ...
expression: r1() && r2() && r3() && r4()
```

对于方式2, 其对应的形式为：

```yaml
rules: 
    r1: ...
    r2: ...
    r3: ...
    r4: ...
expression: (r1() && r2()) || (r3() && r4())
```

甚至我们还能支持：

```yaml
rules: 
    r1: ...
    r2: ...
    r3: ...
    r4: ...
expression: (r1() || r2() || r3()) && r4()
```

这里有几点需要说明一下：

1. rule 的执行顺序是按照该逻辑表达式的执行顺序来执行的
2. `短路求值`, 即 `r1() || r2()`, 如果 `r1()` 的结果为 true 那么 r2 是不会执行的

### set关键字的使用

该字段主要是用来定义一些在接下来的规则中会使用到的一些全局变量，比如随机数，反连平台等。 `set: map[string]interface{}`

```yaml
set:
    a: 1
    num: randonInt(1000, 2000)      # 1543
    rstr: randomLowercase(10)       # asdbeuyekp
    reverse: newReverse()
    reverseURL: reverse.url
```

### payload关键词的使用

该字段用于定义多个 payload，来实现发送不同 payload 的效果。 该字段结构如下

| 变量名/函数名    | 类型               | 说明         |
|------------|------------------|------------|
| `continue` | `bool`           | 命中一个之后是否继续 |
| `payloads` | `map[string]Set` | 和 `set`    |
| 一样的结构和语法   |                  |            |

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

### output与search的组合使用

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
        expression: response.status == 200 && "serial.*?\d+.,".matches(response.body_string) && "date.+?20[0-9][0-9].,".matches(response.body_string)
        output:
            search: '"serial\":\"(?P<serial>.+?)\",\"date".bsubmatch(response.body)'
            serial: search["serial"]        // 4107646840
            search1: '"date\":\"(?P<date>.+?)\",\"macaddr".bsubmatch(response.body)'            
            date: search1["date"]           // 05.07.2022
            HA: serial + "r2d2" + date      // 4107646840r2d205.07.2022
            password: substr(md5(HA),0,7)   // e3048ad
```

## YAML插件的信息部分

该字段用于定义一些和脚本相关的信息。

```yaml
detail:
  author: name(link)
  links:
    - http://example.com
  warning: "警告信息"
  # 指纹信息
  fingerprint:
    infos:
      - id: "长亭指纹库 id"
        name: "SSH"
        version: {{version}}
        type: "system_bin"
        confidence: 70
    host_info:
      hostname: "test"
  # 漏洞信息
  vulnerability:
    id: "长亭漏洞库 id"
    match: "证明漏洞存在的信息"
    # 其它字段
    cve: "CVE-2020-1234"
  # 其它未明确定义的字段
  summary: "test"
```

目前主要定义了一下几个部分：

**注**：其中支持变量渲染，形如 `{{variable}}`, 其中变量为 set 或者 rule output 中定义的变量

- `author: string` 作者
- `links: []string` 相关链接
- `warning: string` 警告信息
-
- `fingerprint` 指纹信息
    - `infos: []Info` 指纹信息
        - `id: string` 长亭指纹库 ID
        - `name: string` 名称
        - `version: string` 版本号
        - `type: string` 指纹类型，有以下可选值： `operating_system`, `hardware`, `system_bin`, `web_application`, `dependency`
        - `confidence: int` 取值范围（1-100）
    - `host_info` 主机信息
        - `hostname: string` 主机名
- `vulnerability` 漏洞信息
    - `id: string` 长亭漏洞库 ID
    - `match: string` 证明漏洞存在的一些信息
    - 额外字段
- 额外字段

warning信息是在这个poc可能会产生某些问题的情况下，对使用者做出提示的字段，例如创建了一个临时文件无法删除，创建了一个账户等


其中author后面跟的link可以填写自己的博客，github主页等信息，也可以不填写。
而links则是填写这个poc的来源地址，或者这个漏洞的相关描述等，方便审核人员审核，也方便扫描出该漏洞的师傅去复现，修复漏洞。
所以在审核的时候，如果链接出现问题，我们也将不会通过。
