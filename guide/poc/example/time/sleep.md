# 函数介绍

`sleep`函数主要是用来暂停执行等待指定的秒数的，一般用在下一个请求需要等待上一个请求的处理时使用

## 函数原型

`func sleep(i1 int) bool`

### 参数介绍

| 参数名称 | 参数介绍 |
|------|------|
| i1   | 等待时常 |

## 举例

```yaml
name: fingerprint-yaml-get-ico
transport: http
rules:
  r0:
    request:
      method: GET
      path: /
      follow_redirects: true
    expression: response.status == 200 && sleep(10)
  r1:
    request:
      method: GET
      path: /
      follow_redirects: true
    expression: response.status == 200
expression: r0()
detail:
  author: test
  links:
    - test.test
```
这样编写后，`r1`规则就会在`r0`执行结束后等待10秒再继续执行