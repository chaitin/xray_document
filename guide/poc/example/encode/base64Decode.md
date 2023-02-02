# 函数介绍

`base64Decode`函数主要是用来将字符串或 bytes 进行 base64 解码，对URL safe 编码做了支持

## 函数原型

`func base64Decode(v1 string/bytes) string`

### 参数介绍

| 参数名称 | 参数介绍  |
|------|-------|
| v1   | 待转换数据 |

## 举例

| 待转换数据                                          | 转换语句                                                           | 输出结果                  |
|------------------------------------------------|----------------------------------------------------------------|-----------------------|
| `SGVsbG8gd29ybGQuIOS9oOWlve+8jOS4lueVjO+8gQ==` | `base64Decode("SGVsbG8gd29ybGQuIOS9oOWlve+8jOS4lueVjO+8gQ==")` | `Hello world. 你好，世界！` |
| `SGVsbG8gd29ybGQuIOS9oOWlve+8jOS4lueVjO+8gQ`   | `base64Decode("SGVsbG8gd29ybGQuIOS9oOWlve+8jOS4lueVjO+8gQ")`   | `Hello world. 你好，世界！` |
| `SGVsbG8gd29ybGQuIOS9oOWlve-8jOS4lueVjO-8gQ==` | `base64Decode("SGVsbG8gd29ybGQuIOS9oOWlve-8jOS4lueVjO-8gQ==")` | `Hello world. 你好，世界！` |
| `SGVsbG8gd29ybGQuIOS9oOWlve-8jOS4lueVjO-8gQ`   | `base64Decode("SGVsbG8gd29ybGQuIOS9oOWlve-8jOS4lueVjO-8gQ")`   | `Hello world. 你好，世界！` |

- **Response**

  ```HTTP
  HTTP/1.1 200 OK
  Content-Length: 223
  Content-Type: text/xml
  
  <?xml version="1.0" encoding="UTF-8"?><methodResponse><params><param><value><base64>cm9vdDp4OjA6MDpyb290Oi9yb290Oi9iaW4vYmFzaApiaW46eDoxOjE6YmluOi9iaW46L3NiaW4vbm9sb2dpbgo=</base64></value></param></params></methodResponse>

  ```
- 匹配语句：`"root:[x*]:0:0:".matches(base64Decode("<base64>(?P<base64>.*)</base64>".bsubmatch(response.body)["base64"]))`
- 输出结果：`true`
