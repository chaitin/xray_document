# 函数介绍

`versionEqual`函数主要是用来比较一个**版本字符串**是否等于所给出的**版本字符串**

## 函数原型

`func (s1 string) versionEqual(s2 string) bool`

### 参数介绍

| 参数名称 | 参数介绍                 |
| -------- | ------------------------ |
| s1       | 需要进行比较的版本字符串 |
| s2       | 需要进行判断的版本字符串 |

## 举例

| 待转换数据 | 转换语句                           | 输出结果 |
| ---------- | ---------------------------------- | -------- |
| `1.8.1`    | `"1.8.1".versionEqual("1.008.01")` | `true`   |

- **Response**

  ```HTTP
  HTTP/1.1 200 OK
  Server: nginx
  Date: Sat, 07 Jan 2023 10:41:06 GMT
  Content-Type: text/html; charset=utf-8
  Connection: close
  Vary: Accept-Encoding
  Cache-Control: no-cache
  Content-Length: 2180

  <!DOCTYPE html>
  <html>
  <head>
      <title>123</title>
  </head>
  <body>
  1.8.1
  </body>
  </html>
  ```

- 匹配语句：`"(?P<version>\d\.\d\.\d\.)".submatch(response.body_string)["version"].versionEqual("1.8.2")`
- 输出结果：`false`
