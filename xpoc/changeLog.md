# xpoc change log

## 0.0.2(2023-05-23)

### 1. 新增特性

- add 命令支持glob语法，可以通过xpoc add -f ./plugin-path 的方式添加插件到插件仓库中，默认条件下会自动加载执行
- -r (-run) 参数支持glob语法，可通过glob语法过滤插件
- 域名目标支持通过-p参数指定端口扫描
- 新增get404Path函数，详情请见[函数介绍](https://docs.xray.cool/#/guide/poc/example/http/get404path)

### 2. 修复问题

- 结果导出选项提示错误的问题，使用-o result.json 或-o result.html 即可导出对应格式的报告。[#2](https://github.com/chaitin/xpoc/issues/2),[#3](https://github.com/chaitin/xpoc/issues/3),[#5](https://github.com/chaitin/xpoc/issues/5)
- 修复部分情况下windows平台下插件加载报错的问题 [#2](https://github.com/chaitin/xpoc/issues/2)
- 修复部分情况下windows平台下启动阶段崩溃的问题 [#1](https://github.com/chaitin/xpoc/issues/1)

### 3. Tips

- -r  (-run) 参数用于执行指定插件，多个插件间','分割 支持glob语法，一般用于测试插件或过滤插件（常用）
- -e (-enable) 参数用于单次扫描添加插件，在默认策略（全量探测）基础上增加本地插件，支持glob语法

## 0.0.1(2023-05-23)

### 1. 功能介绍


1. 拉取所有云端POC并扫描指定目标

    - `./xpoc -t https://example.com -html result.html`

2. 查看所有云端的POC

    - `./xpoc list -a`

3. 批量扫描

    - `./xpoc < targets.txt`
    - ` cat targets.txt | ./xpoc`
    - `./xpoc -i targets.txt`
