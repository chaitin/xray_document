# 提交流程

xray 社区版经过数个版本的更迭，基本覆盖了对常见漏洞的 fuzzing, 稳定性和扫描效果上都有了很大的提高，在此感谢每一位为社区版反馈 bug 和要求功能改进的同学。但是漏洞扫描器这种的安全工具，POC 的质量和数量是一个不可绕过的鸿沟，而个人乃至团队的力量在这种事情面前也是微不足道的，因此，我们希望借助社区的力量来帮助 xray 更快更好的成长。一方面我们会通过大家的反馈对其不断进行迭代优化, 另一方面是集结大家的力量壮大它的 POC 库。

从以往经验来看，POC 宁缺毋滥。一个有问题的 POC 可能会影响到 xray 整体的运行。因此所以我们对社区提交的 POC 有着较为严格的约束。贡献者在提交 POC 的时，需要提供相应的可以复现的测试环境，测试环境以 docker 镜像的方式提供。至于难以做成 docker 镜像的漏洞，可以不提供。

## 规则实验室

xray已经上线了[规则实验室](https://poc.xray.cool)，师傅们可以通过该工具便捷的生成POC，同时可以使用该工具对POC进行格式检查与查重

## 社区提交流程

1. 贡献者可以前往[CT stack](https://stack.chaitin.com)注册账号
2. 然后在安全挑战赛中选择xray扫描POC进行提交，提交地址具体为：[https://stack.chaitin.com/security-challenge/poc/editor](https://stack.chaitin.com/security-challenge/poc/editor)

## Github提交流程

1. 贡献者以 PR 的方式向 github xray 社区仓库内提交， POC 提交位置: [https://github.com/chaitin/xray/tree/master/pocs](https://github.com/chaitin/xray/tree/master/pocs), 指纹识别脚本提交位置: [https://github.com/chaitin/xray/tree/master/fingerprints](https://github.com/chaitin/xray/tree/master/fingerprints)
2. PR 中根据 Pull Request 的模板填写 POC 信息
3. 内部审核 PR，确定是否合并入仓库
4. 但需要注意，如果想要获得POC的奖励，需要将你的POC提交到[CT stack](https://stack.chaitin.com)，才能获取到奖励

注意：

+ 在社区提交时，会先进性格式检测，如果没有通过检查，则会提示错误，可以在[规则实验室](https://poc.xray.cool)或者本地运行 `./xray poclint` 来检查
+ 在 Github 提交 Pull request 后，会有travis-ci自动进行POC的格式检查，对于没有通过的检查，可以通过点击详情查看具体的错误原因：

![](../assets/contribute/fail-detail.jpg)

待修复完成并重新提交达到下面的样子就会被人工审核了。

![](../assets/contribute/pr.jpg)
