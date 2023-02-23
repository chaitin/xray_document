# 函数介绍

`faviconHash`函数主要是用来将字符串或 bytes 进行 faviconHash 编码，参考：[iconhash](https://github.com/Becivells/iconhash)

## 函数原型

`func faviconHash(v1 string/bytes) int`

### 参数介绍

| 参数名称 | 参数介绍  |
|------|-------|
| v1   | 待转换数据 |

## 举例

该函数主要会用在指纹识别这个方向，可以编写一些指纹识别的规则，示例：
```yaml
name: fingerprint-yaml-get-ico
transport: http
rules:
  r0:
    request:
      method: GET
      path: /favicon.ico
      follow_redirects: true
    expression: response.status == 200 && faviconHash(response.body) == xxxxxxx
expression: r0()
detail:
  author: test
  links:
    - test.test
```
其中的xxxxxxx为一个int的数值，表示计算出的hash。

> 该函数一般配合`getIconContent()`一同使用，详情参考：[🔎](guide/poc/example/other/getIconContent.md)