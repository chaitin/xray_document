# 函数介绍

`ibcontains`函数主要是用来对字节流（bytes）中的数据进行匹配，并通过返回一个`bool`的结果来判断是否匹配成功，与bcontains不同的是，ibcontains 忽略大小写

## 函数原型

`func (b1 bytes) ibcontains(b2 bytes) bool`

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

- 匹配语句：`response.body.ibcontains(b"abc123")`
- 输出结果：`true`

- 匹配语句：`response.body.ibcontains(b"abc124")`
- 输出结果：`true`

- 匹配语句：`response.body.ibcontains(b"ASD123")`
- 输出结果：`true`
