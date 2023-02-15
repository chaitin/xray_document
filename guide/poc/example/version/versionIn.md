# 函数介绍

`versionIn`函数主要是用来比较所给出的版本字符串是否在给出的版本范围之内

> 版本格式具体可以参考[semver](https://semver.org/)

## 函数原型

`func versionIn(string, string) bool`

### 参数介绍

| 参数名称 | 参数介绍         |
|------|--------------|
| s1   | 需要进行比较的版本字符串 |
| s2   | 需要进行判断的版本范围  |

## 举例

| 待转换数据           | 转换语句                                                        | 输出结果    |
|-----------------|-------------------------------------------------------------|---------|
| `1.8.1`         | `versionIn("1.8.1", ">= 1.0, <1.8.2")`                      | `true`  |
| `1.8.1`         | `versionIn("1.8.1", "> 1.8.1, < 1.8.2")`                    | `false` |
| `1.8.1-alpha.1` | `versionIn("1.8.1-alpha.1", "> 1.8.1-alpha, < 1.8.1-beta")` | `true`  |
| `1.08.33`       | `versionIn("1.08.33", "> 1.07.4532, < 1.09.001")`           | `true`  |
| `1.08.33`       | `versionIn("1.08.33", "= 1.008.033")`                       | `true`  |

- **Response**

  ```HTTP
  HTTP/1.1 200 OK
  Server: nginx
  Date: Sat, 07 Jan 2023 10:41:06 GMT
  Content-Type: text/html; charset=utf-8
  Connection: close
  Vary: Accept-Encoding
  Cache-Control: no-cache
  Content-Length: 85

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

- 匹配语句：`versionIn("(?P<version>\\d\\.\\d\\.\\d\\.)".submatch(response.body_string)["version"], ">= 1.0, <1.8.2")`
- 输出结果：`true`
