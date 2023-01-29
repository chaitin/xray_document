# 介绍

`reverseType`类型主要是返回了反连平台提供的各个信息，现假设变量名为 `reverse`（需要先使用 `newReverse()` 生成实例)

!> 本文中的`domain`将拿`example.com`作为示例，`ip`将拿`10.1.1.1`作为示例，端口将拿`8899`作为示例

## 类型介绍

| 变量名                             | 类型                        | 说明                            | 适用版本         |
|---------------------------------|---------------------------|-------------------------------|--------------|
| `reverse.url`                   | `urlType`                 | 反连平台的 url                     | xray ≥ 1.8.4 |
| `reverse.domain`                | `string`                  | 反连平台的域名                       | xray ≥ 1.8.4 |
| `reverse.rmi`                   | `urlType`                 | 反连平台的rmi协议url                 | xray ≥ 1.9.4 |
| `reverse.ip`                    | `string`                  | 反连平台的 ip 地址                   | xray ≥ 1.8.4 |
| `reverse.is_domain_name_server` | `bool`                    | 反连平台的 domain 是否同时是 nameserver | xray ≥ 1.8.4 |

## 概念介绍

在reverse生成的不管是子域名还是urlPath，都会有一些像随机数一样的数据：

- `http://10.1.1.1:8899/p/9b8d02/sesI/`
- `p-6ab8d8-fun0.example.com`
- `http://10.1.1.1:8899/i/a8c723/qste/z6f2/`
- `rmi://10.1.1.1:8899/i/hcSvqI7U/b68631/9gir/7yqk/PQ5P8iBh/`
- `http://10.1.1.1:8899/t/449296/docL/0/x.js`

之所以要有这样的path,subdomain，主要是有以下几点原因：

1. 为了区分反连平台收到的请求是否是xray发出的
2. 为了区分不同的请求是由不同插件，不同任务发送的，做好区分
3. 方便异步回调，加快扫描

## 格式介绍

- `http://10.1.1.1:8899/i/a8c723/qste/z6f2/`
  - `i`: 代表该链接是由xray申请产生
  - `a8c723/qste/z6f2`: token，groupId，id的组合
- `p-6ab8d8-fun0.example.com`
  - `p`: 代表该域名是由人在反连平台界面上点击生成
  - `6ab8d8-fun0`: token，groupId的组合
- `http://10.1.1.1:8899/t/449296/docL/0/x.js`
  - `t`: 代表该链接是由模版生成
  - `449296/docL/0`: token，groupId，id的组合
  - `x.js`: 文件名

## 常见使用场景

### reverse.url

- **POC**

  ```yaml
  name: poc-yaml-test
  manual: true
  transport: http
  set:
    reverse: newReverse()
    reverseURL: reverse.url
  rules:
    r0:
      request:
        cache: true
        method: POST
        path: /run
        body: test=ls|curl+{{reverseURL}}
      expression: reverse.wait(5)
  expression: r0()
  detail:
    author: test
    links:
      - http://test.com
  ```

- **构建出的请求**

  ```HTTP
  POST /run HTTP/1.1
  Host: 127.0.0.1
  User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:78.0) Gecko/20100101 Firefox/78.0
  Content-Length: 53
  Accept-Encoding: gzip, deflate
  Connection: close
  
  test=ls|curl+http://10.1.1.1:8899/i/a8c723/qste/z6f2/
  ```

- 在测试目标接收到这样的请求后，如果存在漏洞，则会执行`curl http://10.1.1.1:8899/i/a8c723/qste/z6f2/`这样一条命令，这时我们的反连平台就会收到该请求，确认漏洞存在。

### reverse.domain

- **POC**

  ```yaml
  name: poc-yaml-test
  manual: true
  transport: http
  set:
    reverse: newReverse()
    reverseDomain: reverse.domain
  rules:
    r0:
      request:
        cache: true
        method: POST
        path: /run
        body: test=ls|ping+{{reverseDomain}}
      expression: reverse.wait(5)
  expression: r0()
  detail:
    author: test
    links:
      - http://test.com
  ```

- **构建出的请求**

  ```HTTP
  POST /run HTTP/1.1
  Host: 127.0.0.1
  User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:78.0) Gecko/20100101 Firefox/78.0
  Content-Length: 43
  Accept-Encoding: gzip, deflate
  Connection: close
  
  test=ls|ping+i-eeec7b-sna3-2r77.example.com
  ```

- 在测试目标接收到这样的请求后，如果存在漏洞，则会执行`ping i-eeec7b-sna3-2r77.example.com`这样一条命令，这时我们的反连平台就会收到该请求，确认漏洞存在。