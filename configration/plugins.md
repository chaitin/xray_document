插件配置这一部分对应于配置文件的 plugins 部分。这一部分的每个配置项的 key 是插件名称，value 是与该插件相关的配置。
每个部分的结构大致如下

```yaml
pluginName:
    enabled: true/false
    otherConfigrations: xxx
```

`enabled` 即为是否启用插件, 其它的配置如果有，则是当前插件的一些特殊配置。鉴于配置文件本身已经有很多注释，这里不做赘述，仅对几个比较特殊的配置展开说明下。

### xss

+ `ie_feature` 如果此项为 true，则会将一些只能在 IE 环境下复现的漏洞爆出来，小白请不要开。主要包括 expression xss、hidden tag xss、utf-7 和 content type sniffing 导致的 xss （[参考链接](https://mp.weixin.qq.com/s?__biz=MzI5MzY2MzM0Mw==&mid=2247484070&idx=1&sn=673e20a08d9ae6c3de60ca48110b920a) 中的 `3. json`）。
+ `include_cookie` 如果此项为 true, 则会检查是否存在输入源在 cookie 中的 xss

### dirscan
+ `depth` 深度限制
+ `dictionary` 配置目录字典, 需要是绝对路径, 配置后将与内置字典**合并**

`depth` 是探测深度, 默认为 1, 即只在 URL 深度为 0, 和深度为 1 时运行该插件（前提是启用了该插件)

定义 http://example.com/，深度为 0，定义 http://example.com/a/, 深度为 1。 在这种配置下，dirscan 将对 

http://example.com/ 和 http://example.com/a/ 做两次扫描，如果存在 http://example.com/a/example.zip 那么就能将其扫描出来。

### sqldet:

下面这个选项很危险，开启之后可以增加检测率，但是有破坏数据库数据的可能性，请务必了解工作原理之后再开启
+ `dangerously_use_comment_in_sql` 允许检查注入的时候使用注释

### phantasm

phantasm 是 xray 的 poc 框架，在其下运行着许多 yaml 和 go 写的 poc，用户可以通过该模块编写自己的 poc 并让 xray 加载，具体见后续 [自定义POC语法](guide/poc.md)

这里我们先介绍一下它的两个重点配置:

```yaml
depth: 1                            # 与 dirscan 一样，不再赘述
auto_load_poc: false                # 除内置 poc 外，额外自动加载当前目录以 "poc-" 为文件名前缀的POC文件，等同于在 include_poc 中增加 "./poc-*"
exclude_poc: []                     # 排除哪些 poc, 支持 glob 语法, 如: /home/poc/*thinkphp* 或 poc-yaml-weblogic*
include_poc: []                     # 只使用哪些内置 poc 以及 额外加载哪些本地 poc, 支持 glob 语法, 如："*weblogic*" 或 "/home/poc/*"
                                    # 也可使用 --poc 仅运行 指定的内置或本地 poc，进行测试。
                                    # 例如，可使用如下命令，仅运行当前目录下的 poc 且 不运行内置 poc 进行测试：
                                    # webscan -poc ./poc-* -url http://example.com
poc_tags:                           # 为POC添加tag, 然后就可以使用--tags来选择启动哪些POC。poc-yaml-test为poc的name，[]中的内容为该POC对应的标签
  poc-yaml-test: ["HW", "ST"]
  poc-yaml-test-1: ["ST"]    
```

`exclude_poc` 用于去除加载哪些 poc。一个常见的 case 是如果发现某些 poc 误报比较多，想暂时禁用掉（并反馈给 xray)，那么就可以在这一个配置中加上 poc 的名字，比如:

```yaml
plugins:
  ...
  phantasm:
    enabled: true
    exclude_poc:
    - poc-yaml-bad-poc
    - *bad-poc*
```

`include_poc` 是用于加载本地的 poc 的配置，最好指定绝对路径，且同样支持 glob 语法。

一个稍微复杂的情况是将这两个搭配起来使用，比如:

```yaml
plugins:
  ...
  phantasm:
    enabled: true
    exclude_poc:
    - /home/poc/poc-fake-good-poc
    include_poc:
    - /home/poc/*good-poc*
```

上述配置的意思是加载 `/home/poc/` 目录下所有符合 `*good-poc*` 这个pattern 的poc，同时去掉同样目录下的 `poc-fake-good-poc`

`poc_tags` 是用于对内置的POC进行打标签，`poc-yaml-test`为poc的name，`["HW", "ST"]`为该POC的标签。

使用示例如下：
`./xray ws --tags ST,HW`
这个时候，xray将会加载带有ST标签的POC。

注：--tags可以与--level，--poc同时使用


### xstream

在`xstream`模块中，有一个特殊选项：`full`

一般情况下`xml`请求是通过`POST`方法的`Body`发送，但特殊情况下可能会通过普通的某个参数发送。由于这种情况需要发送大量的数据包，所以默认关闭。如果你确定要使用这种测试方法，可以设置`full`参数为`true`

```yaml
  xstream:
    enabled: true
    full: false
```

注：如果你发现`XStream`模块的`PoC`和对应`CVE`不符，这是正常情况。`XStream`历史上有很多的`CVE`漏洞，我们的检测逻辑是当某一个版本存在多个`CVE`漏洞时，只要能够检测到其中一个漏洞就会输出当前版本存在的所有漏洞

由于我们使用反连来确认`XStream`漏洞，所以该模块几乎不会产生误报