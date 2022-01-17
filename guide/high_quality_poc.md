# 如何编写高质量的 script

## 一定不要这么做

1. 无特殊情况不要在payload中出现你的用户名或者xray等字样，能随机的都随机，否则有些特征可能会被 waf 加入规则库拦截。
1. 不要直接使用如下判断
    1. 尽量不要仅使用单一条件来判断，比如只判断 `response.status` 或者 `response.content_type`会造成误报
    1. 不要使用容易变化的值来判断，比如 `content length`， 会造成漏报
    1. 不要使用容易出现的值来判断, 比如
        1. `response.body.bcontains(b'upload success')` 会造成误报
        1. 计算了一个空页面/空图片的md5值用于判断

## 约定做法

1. 变量一律采用驼峰命名, 一些固定变量命名方式保持一致， 如反连平台相关变量
    1. `reverse: newReverse`
    1. `reverseURL: reverse.url`
    1. `reverseDomain: reverse.domain`
    1. `reverseIP: reverse.ip`
1. 尽量将 payload 单独抽离出来作为变量定义，可以方便后续对于 payload 进行增删改

## 推荐场景

- [HTTP PATH 的使用](guide/skill/path)
- [代码执行](guide/skill/rce)
- [PAYLOAD 的使用](guide/skill/payload)
- [反连平台的使用](guide/skill/reverse)
- [头疼的转义](guide/skill/escape)

## 漏洞探测篇

### 不要

1. 测试RCE类漏洞，如PHP代码执行，请不要使用`system`、`shell_exec`、`phpinfo`等函数测试漏洞，容易出现误报和漏报，原因如下：
    1. 如果对方本身就是一个phpinfo页面，无法判断是否是成功执行了代码，导致出现误报
    1. 如果对方网站运行在一些虚拟主机环境下，如cpanel，则命令执行函数很可能已经被禁用，此时再用`system`等函数测试漏洞则会出现漏报
1. 测试命令执行、文件读取类漏洞，请考虑Linux和Windows下的情况，POC里不要执行类似`id`、`uname`、`/etc/passwd`等操作，否则可能不兼容Windows环境。通常这种情况，我们可以考虑编写两个POC，分别检测两个平台下的同一个漏洞
    1. 匹配 `/etc/passwd` 文件的方法为 `"root:[x*]:0:0:".bmatches(response.body)`，但部分比较特殊的 linux 系统不是这种规则，需要自行确认下。
1. 测试SQL注入漏洞，请不要使用`user()`、`version()`等函数来验证漏洞存在，此类规则太过宽泛。请使用如`select md5(随机数)`、`updatexml`、`extractvalue` 等方式来验证漏洞。目前可能有以下例外需要注意下
    1. `updatexml`、`extractvalue` 报错回显，请注意 md5 可能会被截断，可以使用 `response.body.bcontains(bytes(substr(md5(string(r1)), 0, 31)))` 的截取字符串函数。
1. Payload中尽量不要使用引号、反斜线（除非是必要的）等特殊字符，可能会受到目标`GPC_QUOTE`、WAF等影响。
    1. 如果是Mysql，可以使用`0xxxx`的方式代替有引号的字符串
    1. php的md5可以使用数字作为参数，会转换为字符串进行计算的。但是xray的md5需要手动转换为字符串，比如 `md5(string(10000))`
1. 漏洞如果能通过回显检测，就不要使用反连平台，鼓励将公开的无回显的POC改为有回显的。比如公开的struts2漏洞POC很多是反弹shell的POC，但几乎所有struts2的POC都可以修改为有回显的POC。
1. 测试RCE类漏洞，请不要使用`echo`、`print`、`var_dump`之类的输出语句直接输出一个内容，然后在返回里查找这个内容，此类POC很容易误报和漏报，原因如下：
    1. 如果对方页面本身是一个类似phpinfo的调试页面，会将你的数据包细节完全打印出来，那么并不能证明存在命令执行漏洞
    1. 如果对方安装了xdebug等调试类扩展，`var_dump`等函数输出可能存在差异导致查找不成功
  
    以下就是一个例子，如果页面显示是 `/admin/?a=Factory();printf('{{r1}}');//../ 404 not found` 那就会误报。
    ```yaml
        method: GET
        path: /admin/?a=Factory();printf('{{r1}}');//../
        follow_redirects: false
        expression: |
            response.status == 200 && response.body.bcontains(bytes(r1))
    ```

### 应该

1. 各种 rce 通常都可以直接使用整数相乘相加或者md5的方法，然后再查找返回结果，这样只有在代码真正被执行的时候才会得到预期的结果。
    1. 如果是测试命令执行，目前推荐使用 `expr num1 + num2`，主要因为星号在 shell 中可能有潜在的转义问题，执行其他的命令可能返回值格式并不方便匹配。
    1. 如果是 php 等代码执行，可以使用
        1. print(数字 * 数字)
        1. print(md5(随机值))
        1. printf("随机值%%随机值") 实际输出会少一个百分号
    1. sql 注入也可以使用 `union select md5(随机值)` 的原理
    1. 考虑到有些32位系统整数上限可能低于`2^^31`和数字过短可能误报，目前要求乘法两个数字的取值范围必须在 `40000` 和 `44800` 之间，加法两个数字必须在 `800000000` 和 `1000000000` 之间。

    ```yaml
    name: poc-yaml-bash-cve-2014-6271
    set:
    r1: randomInt(800000000, 1000000000)
    r2: randomInt(800000000, 1000000000)
    rules:
    - method: GET
        headers:
        User-Agent: "() { :; }; echo; echo; /bin/bash -c 'expr {{r1}} + {{r2}}'"
        expression: response.body.bcontains(bytes(string(r1+r2)))
    detail:
    author: example(https://github.com/example)
    links:
        - https://github.com/opsxcq/exploit-CVE-2014-6271
    ```

1. 上传类 poc 测试希望上传的文件由两部分构成，一是 echo/print 一串字符串用于确认漏洞存在，二是执行自删除逻辑，删除测试产生的垃圾文件。可以参考: http://doc.bugscan.net/chapter3/3-3.html
1. 关于POC的编写其他的注意点，推荐参考这篇文章：<https://paper.seebug.org/9/>
