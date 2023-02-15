# 函数介绍

`submatch`函数主要是用来使用正则表达式 s1 来匹配 s2，返回 map[string]string 类型结果，**注**：只返回具名的正则匹配结果 (?P<name>…) 格式

## 函数原型

`func (s1 string) submatches(s2 string) map[string]string`

### 参数介绍

| 参数名称 | 参数介绍   |
|------|--------|
| s1   | 正则匹配语句 |
| s2   | 待匹配的数据 |

## 命名捕获组

- 在使用submatch的时候，会与一个正则语法，"命名捕获组"一同使用：`(?P<name>pattern)`
- 其中 name 是捕获组的名称，pattern 是要匹配的模式。捕获组是一种可以将匹配的文本捕获并在后续操作中使用的机制。
- 在yaml中，我们将name化作map的key，pattern匹配到的内容化作value，也就是说，在一段正则中，可以存在多个命名捕获组，例如：
  - `"^SSH-([\\d.]+)-OpenSSH_(?P<version0>[\\w._-]+) Debian-(?P<version1>\\S*maemo\\S*)\\r?\\n"`
  - 可以看到上方的例子中的`\w`、`\d`、`\n`等都被替换成了`\\w`、`\\d`、`\\n`，这是因为需要规避yaml的转译


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
  Token: aver258wniuv15ngfverw378cas
  Content-Length: 85

  <!DOCTYPE html>
  <html>
  <head>
      <title>123</title>
  </head>
  <body>
  version: 1.8.1
  </body>
  </html>
  ```
- 可以编写如下脚本判断版本：
  ```yaml
  name: poc-yaml-openssl-cve-2022-3602-3786
  transport: http
  rules:
    r0:
      request:
        method: GET
        path: /
        follow_redirects: false
        expression: response.status == 200 && "(?P<version>\\d\\.\\d\\.\\d\\.)".submatch(response.body_string)["version"].versionEqual("1.8.1")
      expression: r0()
  detail:
    author: test
    links:
      - test.test
  ```
- 如果是要提供给下一个规则，则可以使用output：
  ```yaml
  name: poc-yaml-openssl-cve-2022-3602-3786
  transport: http
  rules:
    r0:
      request:
        method: GET
        path: /
        follow_redirects: false
        expression: response.status == 200
      expression: r0()
      output:
        token: '"(?<token>\\w*)".submatch(response.headers["Token"])["token"]'
    r1:
      request:
        method: GET
        path: /{{token}}
        follow_redirects: false
        expression: response.status == 200
      expression: r0()
  detail:
    author: test
    links:
      - test.test
  ```
- 其中上述的token还可以拆分成serach和token，也就是：

  ```yaml
  search: '"(?<token>\\w*)".submatch(response.headers["Token"])'
  token: search["token"]
  ```