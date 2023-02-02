# 函数介绍

`bstartsWith`函数主要是用来判断一个 b1 是否由 b2 开头，返回 bool 类型结果。与 startsWith 不同的是，bcontains 是字节流（bytes）的查找

## 函数原型

`func (b1 bytes) bstartsWith(b2 bytes) bool`

### 参数介绍

| 参数名称 | 参数介绍   |
|------|--------|
| b1   | 被匹配的数据 |
| b2   | 要匹配的数据 |

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

- 匹配语句：`response.body.bstartsWith(b"<!DOCTYPE")`
- 输出结果：`true`
