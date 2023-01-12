# xray 脚本注入的类型

xray 支持所有CEL文档中的类型，同时还注入了几种特殊的类型，包含：

- `addrType` 连接地址信息
- `connInfoType` 连接信息，包含源地址和目的地址, 可以通过 `response.conn`
- `urlType` url 类型，可以 `request.url`、`response.url` 和 `reverse.url` 调用
- `reverseType` 反连平台类型
- `request` 扫描请求
- `response` 请求的响应，通用属性包含：`raw`

其中注入的 request 和 response 类型是随着 `transport` 对应的值进行改变的。

## 基本类型

addrType 类型包含字段如下, 设变量名为 `addr`

| 变量名              | 类型       | 说明                                                        | 适用版本         |
|------------------|----------|-----------------------------------------------------------|--------------|
| `addr.transport` | `string` | tranport                                                  | xray ≥ 1.8.4 |
| `addr.addr`      | `string` | 目的地址， 获取失败时返回空字符串，形如： `192.0.2.1:25`, `[2001:2001::1]:80` | xray ≥ 1.8.4 |
| `addr.port`      | `string` | 端口号， 获取失败时返回 `""`                                         | xray ≥ 1.8.4 |

connInfoType 类型包含字段如下, 设变量名为 `conn`

| 变量名                | 类型         | 说明     | 适用版本         |
|--------------------|------------|--------|--------------|
| `conn.source`      | `addrType` | 源地址信息  | xray ≥ 1.8.4 |
| `conn.destination` | `addrType` | 目的地址信息 | xray ≥ 1.8.4 |

urlType 类型包含的字段如下, 设变量名为 `url`, 以 `http://example.com:8080/a?c=d#x=y` 为例:

| 变量名            | 类型       | 说明                                 | 适用版本         |
|----------------|----------|------------------------------------|--------------|
| `url.scheme`   | `string` | url 的 scheme, 示例为 `"http"`         | xray ≥ 1.8.4 |
| `url.domain`   | `string` | url 的域名，示例例为 `"example.com"`       | xray ≥ 1.8.4 |
| `url.host`     | `string` | url 的主机名，示例为 `"example.com:8080"`  | xray ≥ 1.8.4 |
| `url.port`     | `string` | url 的 port，注意这里也是字符串。 示例为 `"8080"` | xray ≥ 1.8.4 |
| `url.path`     | `string` | url 的 path， 示例为 `"/a"`             | xray ≥ 1.8.4 |
| `url.query`    | `string` | url 的 query, 示例为 `"c=d"`           | xray ≥ 1.8.4 |
| `url.fragment` | `string` | url 的锚点，示例为 `"x=y"`                | xray ≥ 1.8.4 |

reverseType 类型包含字段如下, 设变量名为 `reverse`（需要先使用 `newReverse()` 生成实例)

| 变量名                             | 类型                        | 说明                            | 适用版本         |
|---------------------------------|---------------------------|-------------------------------|--------------|
| `reverse.url`                   | `urlType`                 | 反连平台的 url                     | xray ≥ 1.8.4 |
| `reverse.domain`                | `string`                  | 反连平台的域名                       | xray ≥ 1.8.4 |
| `reverse.rmi`                   | `urlType`                 | 反连平台的rmi协议url                 | xray ≥ 1.9.4 |
| `reverse.ip`                    | `string`                  | 反连平台的 ip 地址                   | xray ≥ 1.8.4 |
| `reverse.is_domain_name_server` | `bool`                    | 反连平台的 domain 是否同时是 nameserver | xray ≥ 1.8.4 |
| `reverse.wait(timeout)`         | `func (timeout int) bool` | 等待 timeout 秒，并返回是否在改时间内获得了信息  | xray ≥ 1.8.4 |

## tcp request and response

其中 request 包含的字段如下：

| 变量名           | 类型       | 说明   | 适用版本         |
|---------------|----------|------|--------------|
| `request.raw` | `[]byte` | 原始请求 | xray ≥ 1.8.4 |

response 包含的字段如下:

| 变量名             | 类型             | 说明     | 适用版本         |
|-----------------|----------------|--------|--------------|
| `response.conn` | `connInfoType` | 连接相关信息 | xray ≥ 1.8.4 |
| `response.raw`  | `[]byte`       | 原始响应   | xray ≥ 1.8.4 |

## udp request and response

其中 request 包含的字段如下：

| 变量名           | 类型       | 说明   | 适用版本         |
|---------------|----------|------|--------------|
| `request.raw` | `[]byte` | 原始请求 | xray ≥ 1.8.4 |

response 包含的字段如下:

| 变量名             | 类型             | 说明     | 适用版本         |
|-----------------|----------------|--------|--------------|
| `response.conn` | `connInfoType` | 连接相关信息 | xray ≥ 1.8.4 |
| `response.raw`  | `[]byte`       | 原始响应   | xray ≥ 1.8.4 |

## http request and response

其中 request 包含的字段如下：

| 变量名                    | 类型                  | 说明                                                                                                                                      | 适用版本         |
|------------------------|---------------------|-----------------------------------------------------------------------------------------------------------------------------------------|--------------|
| `request.raw`          | `[]byte`            | 原始请求                                                                                                                                    | xray ≥ 1.8.4 |
| `request.url`          | `urlType`           | 自定义类型 urlType, 请查看下方 urlType 的说明                                                                                                        | xray ≥ 1.8.4 |
| `request.method`       | `string`            | 原始请求的方法                                                                                                                                 | xray ≥ 1.8.4 |
| `request.headers`      | `map[string]string` | 原始请求的HTTP头，是一个键值对（均为小写），我们可以通过`headers['server']`来获取值。如果键不存在，则获取到的值是空字符串。注意，该空字符串不能用于 `==` 以外的操作，否则不存在的时候将报错，需要先 `in` 判断下。详情参考下文常用函数章节。 | xray ≥ 1.8.4 |
| `request.content_type` | `string`            | 原始请求的 content-type 头的值, 等于`request.headers["Content-Type"]`                                                                             | xray ≥ 1.8.4 |
| `request.raw_header`   | `[]byte`            | 原始的 header 部分，需要使用字节流相关方法来判断。                                                                                                           | xray ≥ 1.8.4 |
| `request.body`         | `[]byte`            | 原始请求的 body，需要使用字节流相关方法来判断。如果是 GET， body 为空。                                                                                             | xray ≥ 1.8.4 |

response 包含的字段如下:

| 变量名                     | 类型                  | 说明                                                 | 适用版本         |
|-------------------------|---------------------|----------------------------------------------------|--------------|
| `response.raw`          | `[]byte`            | 原始响应                                               | xray ≥ 1.8.4 |
| `response.url`          | `urlType`           | 自定义类型 urlType, 请查看下方 urlType 的说明                   | xray ≥ 1.8.4 |
| `response.status`       | `int`               | 返回包的status code                                    | xray ≥ 1.8.4 |
| `response.raw_header`   | `[]byte`            | 原始的 header 部分，需要使用字节流相关方法来判断。                      | xray ≥ 1.8.4 |
| `response.body`         | `[]byte`            | 返回包的Body，因为是一个字节流（bytes）而非字符串，后面判断的时候需要使用字节流相关的方法  | xray ≥ 1.8.4 |
| `response.body_string`  | `string`            | 返回包的Body，是一个字符串                                    | xray ≥ 1.9.1 |
| `response.headers`      | `map[string]string` | 返回包的HTTP头，类似 `request.headers`。                    | xray ≥ 1.8.4 |
| `response.content_type` | `string`            | 返回包的content-type头的值                                | xray ≥ 1.8.4 |
| `response.latency`      | `int`               | 响应的延迟时间，可以用于 sql 时间盲注的判断，单位毫秒 (ms)                 | xray ≥ 1.8.4 |
| `response.title`        | `[]byte`            | 返回包的Title，因为是一个字节流（bytes）而非字符串，后面判断的时候需要使用字节流相关的方法 | xray ≥ 1.8.5 |
| `response.title_string` | `string`            | 返回包的Title，是一个字符串                                   | xray ≥ 1.8.5 |
