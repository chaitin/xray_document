# 函数介绍

`contains`函数主要是用来对字符串中的数据进行匹配，并通过返回一个`bool`的结果来判断是否匹配成功

## 函数原型

`func (s1 string) contains(s2 string) bool`

### 参数介绍

| 参数名称 | 参数介绍   |
|------|--------|
| s1   | 被匹配的数据 |
| s2   | 要匹配的数据 |

## 举例

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
      <title>abc123</title>
  </head>
  <body>
  asd123
  ABC124
  </body>
  </html>
  ```
  
- 匹配语句：`response.body_string.contains("abc123")`
- 输出结果：`true`

- 匹配语句：`response.body_string.contains("asd123")`
- 输出结果：`true`

- 匹配语句：`response.body_string.contains("abc124")`
- 输出结果：`false`
