这一部分主要介绍配置项中 `mitm` 部分相关的内容。

```yaml
mitm:
  ca_cert: ./ca.crt                     # CA 根证书路径
  ca_key: ./ca.key                      # CA 私钥路径
  basic_auth:                           # 基础认证的用户名密码
    username: ""
    password: ""
  allow_ip_range: []                    # 允许的 ip，可以是 ip 或者 cidr 字符串
  restriction:                          # 代理能够访问的资源限制, 以下各项为空表示不限制
    hostname_allowed: []                # 允许访问的 Hostname，支持格式如 t.com、*.t.com、1.1.1.1、1.1.1.1/24、1.1-4.1.1-8
    hostname_disallowed:                # 不允许访问的 Hostname，支持格式如 t.com、*.t.com、1.1.1.1、1.1.1.1/24、1.1-4.1.1-8
    - '*google*'
    - '*github*'
    - '*.gov.cn'
    - '*chaitin*'
    - '*.xray.cool'
    port_allowed: []                    # 允许访问的端口, 支持的格式如: 80、80-85
    port_disallowed: []                 # 不允许访问的端口, 支持的格式如: 80、80-85
    path_allowed: []                    # 允许访问的路径，支持的格式如: test、*test*
    path_disallowed: []                 # 不允许访问的路径, 支持的格式如: test、*test*
    query_key_allowed: []               # 允许访问的 Query Key，支持的格式如: test、*test*
    query_key_disallowed: []            # 不允许访问的 Query Key, 支持的格式如: test、*test*
    fragment_allowed: []                # 允许访问的 Fragment, 支持的格式如: test、*test*
    fragment_disallowed: []             # 不允许访问的 Fragment, 支持的格式如: test、*test*
    post_key_allowed: []                # 允许访问的 Post Body 中的参数, 支持的格式如: test、*test*
    post_key_disallowed: []             # 不允许访问的 Post Body 中的参数, 支持的格式如: test、*test*
  queue:
    max_length: 3000                    # 队列长度限制, 也可以理解为最大允许多少等待扫描的请求, 请根据内存大小自行调整
  proxy_header:
    via: ""                             # 是否为代理自动添加 Via 头
    x_forwarded: false                  # 是否为代理自动添加 X-Forwarded-{For,Host,Proto,Url} 四个 http 头
  upstream_proxy: ""                    # 为 mitm 本身配置独立的代理
```

## 代理启用密码保护
对应于 `auth` 中的配置。

xray 支持给代理配置基础认证的密码，当设置好 `auth` 中的 `username` 和 `password` 后，使用代理时浏览器会弹框要求输出用户名密码，输入成功后代理才可正常使用。

## 限制允许使用该代理的 IP

配置中的 `allow_ip_range` 项可以限制哪些 IP 可以使用该代理。支持单个 IP 和 CIDR 格式的地址，如：
```yaml
allow_ip_range: ["127.0.0.1","192.168.1.1/24"]
```
留空则允许所有地址访问，如果来源 IP 没有在配置的地址内，使用者会显示这样的错误:
![ip_range.jpg](../assets/configuration/allow_ip_range.jpg)

## 队列长度配置

```yaml
  queue:
    max_length: 3000
```

在做被动扫描时，xray 收到一个请求可能要发出去上百个请求进行扫描，所以获取请求和消耗请求的速度不一定匹配，这是一个经典的生产者消费者问题，如果生产消费速度不匹配，就需要一个中间的队列来临时存储，这个队列的大小就是 `max_length`。

xray 将每 10s 打印一下当前任务队列长度，一旦堆积的数量达到 `max_length`，代理将会 “卡住”，新请求无法通过，等待队列中的请求被处理后再继续生效。默认配置的 10000 表示最多允许堆积 10000 个请求。

如果发现队列长度经常变满，可能是扫描速度太慢，可以尝试减少请求超时的时间和增加最大并发请求数，详见 `http` 配置章节。

如果 `max_length` 设置的过大，会造成 xray 内存占用过大，甚至可能会造成内存不足 OOM 进程崩溃。比如我们假设一个 http 请求加响应为 20kb，那 3000 个请求理论上内存占用至少为 60Mb，实际场景下可能会比理论值还要大很多。

更多信息可以参见[扫描速度](guide/speed)。

## 代理请求头配置 `proxy_header`

```
proxy_header:
    via: "" # 如果不为空，proxy 将添加类似 Via: 1.1 $some-value-$random 的 http 头
    x_forwarded: false # 是否添加 X-Forwarded-{For,Host,Proto,Url} 四个 http 头
```

如果开启 proxy_header，代理会添加 `via` 头和 `X-Forwarded-*` 系列头。如果在请求中就已经存在了同名的 HTTP 头，那么将会追加在后面。

比如 `curl http://127.0.0.1:1234 -H "Via: test" -H "X-Forwarded-For: 1.2.3.4" -v`，后端实际收到的请求将会是

```http
GET / HTTP/1.1
Host: 127.0.0.1:1234
User-Agent: curl/7.54.0
Accept: */*
Via: test, 1.1 xray-1fe7f9e5241b2b150f32
X-Forwarded-For: 1.2.3.4, 127.0.0.1
X-Forwarded-Host: 127.0.0.1:1234
X-Forwarded-Proto: http
X-Forwarded-Url: http://127.0.0.1:1234/
Accept-Encoding: gzip
```

## 代理的代理 `upstream_proxy`

!> 该项配置仅对 http 代理本身生效，不对漏洞扫描发出的请求生效。如果想配置漏洞扫描时的代理，请参照配置文件 `http` 部分的配置。

假如启动 xray 时配置的 listen 为 `127.0.0.1:1111`，`upstream_proxy` 为 `http://127.0.0.1:8080`, 那么浏览器设置代理为 `http://127.0.0.1:1111`，整体数据流如下:

![](../assets/configuration/upstream.jpg)

再次重申下，该配置仅影响代理本身，不会影响插件在漏洞探测时的发包行为，如果想代理漏洞探测的，请参照接下来的 [HTTP配置](./http.md) 的部分!
