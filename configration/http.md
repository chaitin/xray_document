对于 web 扫描来说，http 协议的交互是整个过程检测过程的核心。因此这里的配置将影响到引擎进行 http 发包时的行为。

```yaml
http:
  proxy: ""                             # 漏洞扫描时使用的代理，如: http://127.0.0.1:8080。 如需设置多个代理，请使用 proxy_rule 或自行创建上层代理
  proxy_rule: []                        # 漏洞扫描使用多个代理的配置规则, 具体请参照文档
  dial_timeout: 5                       # 建立 tcp 连接的超时时间
  read_timeout: 10                      # 读取 http 响应的超时时间，不可太小，否则会影响到 sql 时间盲注的判断
  max_conns_per_host: 50                # 同一 host 最大允许的连接数，可以根据目标主机性能适当增大
  enable_http2: false                   # 是否启用 http2, 开启可以提升部分网站的速度，但目前不稳定有崩溃的风险
  fail_retries: 0                       # 请求失败的重试次数，0 则不重试
  max_redirect: 5                       # 单个请求最大允许的跳转数
  max_resp_body_size: 2097152           # 最大允许的响应大小, 默认 2M
  max_qps: 500                          # 每秒最大请求数
  allow_methods:                        # 允许的请求方法
  - HEAD
  - GET
  - POST
  - PUT
  - PATCH
  - DELETE
  - OPTIONS
  - CONNECT
  - TRACE
  - MOVE
  - PROPFIND
  headers:
    User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:78.0) Gecko/20100101 Firefox/78.0
    # Cookie: key=value
```

## 漏洞扫描用的代理 `proxy`

配置该项后漏洞扫描发送请求时将使用代理发送，支持 `http`, `https` 和 `socks5` 三种格式，如:

```
http://127.0.0.1:1111
https://127.0.0.1:1111
socks5://127.0.0.1:1080
```

如果代理需要认证，可以使用下面的格式 `http://user:password@127.0.0.1:1111`

## 多代理配置

在漏洞扫描的时候，可能想不同的域名使用不同的代理，设置多个代理切换等，可以通过 `proxy_rule` 字段来配置。需要注意的是，`proxy` 配置将优先于本配置。

```
proxy_rule:
  - match: "*host1"
    servers:
      - addr: "http://127.0.0.1:8001"
        weight: 1
      - addr: "http://127.0.0.1:8002"
        weight: 2
  - match: "*"
    servers:
      - addr: "http://127.0.0.1:8003"
        weight: 1
      - addr: "http://127.0.0.1:8004"
        weight: 5
```

 - match: 请求的 url 的主机名如果匹配，就使用本条规则。
   - 如果是 `*`，则代表可以匹配所有。所以一定要将 `*` 放在最后面，上面没有匹配到的域名都将使用这个配置。
   - 如果没有任何一条可以匹配，这个请求将不会使用代理。
 - addr: 代理服务器的地址，同 `proxy` 的配置。
 - weight: 代理服务器的权重，如果 `servers` 中配置了多个代理服务器，设置权重可以均衡负载，比如权重是 `3:7`，则代表每 10 个请求，有 3 个选择 server1，有 7 个选择 server2。要注意的是，这里是 round bin 算法，前 3 个一定发往 server1，后面 7 个一定发往 server2，然后继续循环，不是每个请求都是基于权重随机的。

## 限制发包速度 `max_qps`

默认值 500， 因为最大允许每秒发送 500 个请求。一般来说这个值够快了，通常是为了避免被ban，会把该值改的小一些，极限情况支持设置为 1， 表示每秒只能发送一个请求。

## 其他配置项

对照配置文件的注释好好理解下应该就能懂，如果不懂就不要改了。
