# 函数介绍

`matches`函数主要是用来使用正则表达式 s1 来匹配 s2，并返回 bool 类型匹配结果的

## 函数原型

`func (s1 string) matches(s2 string) bool`

### 参数介绍

| 参数名称 | 参数介绍   |
|------|--------|
| s1   | 匹配语句   |
| s2   | 待匹配的内容 |

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
  Content-Length: 373
  
  root:x:0:0:root:/root:/bin/bash
  daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
  bin:x:2:2:bin:/bin:/usr/sbin/nologin
  sys:x:3:3:sys:/dev:/usr/sbin/nologin
  sync:x:4:65534:sync:/bin:/bin/sync
  games:x:5:60:games:/usr/games:/usr/sbin/nologin
  man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
  lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
  mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
  ```
- 匹配语句：`"root:.*?:[0-9]*:[0-9]*:".matches(response.body_string)`
- 输出结果：`true`

- 匹配语句：`"Accept-Enco.*".matches(response.headers["Vary"])`
- 输出结果：`true`