# 函数介绍

`getIconContent`函数主要是用来

## 函数原型

`func (r1 response) getIconContent() bytes`

### 参数介绍

| 参数名称 | 参数介绍 |
|------|------|
| r1   | 响应包  |

## 举例

- 该函数会对响应包中的icon数据进行进行匹配解码，并返回icon的字节流数据。
- 如果响应包中不存在icon数据，则会查找是否存在记录了相关icon链接的内容，如果存在，则会尝试访问该链接，并获取icon的字节流数据
- 该函数一般搭配`faviconHash()`一同使用，具体示例如下，测试地址为`https://docs.xray.cool`

```yaml
name: poc-yaml-test
transport: http
rules:
  r0:
    request:
      method: GET
      path: /
      follow_redirects: false
    expression: response.status == 200 && faviconHash(response.getIconContent()) == 938617678
expression: r0()
detail:
  author: test
  links:
    - test
```

