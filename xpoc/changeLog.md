# xpoc change log

## 0.0.8(2023-08-11)

### 新增特性

1. 新增QPS参数配置
2. 增加了[rsaEncryptPKCS1v15](guide/poc/example/encryption/rsaPKCS1v15.md)和[rsaDecryptPKCS1v15](guide/poc/example/encryption/rsaPKCS1v15.md)函数
3. 增加了[zipSlip](guide/poc/example/other/zipSlip.md) 函数
4. 新增[aesDecrypt](guide/poc/example/encryption/aes.md)函数

### 修复与优化

1. 修复反连平台存在的问题
2. 优化服务识别结果展示
3. 修复yaml插件超时相关配置不生效的问题
4. 优化list命令，缩短md5展示长度

## 0.0.7(2023-07-21)

### 新增特性

支持在配置文件中配置自定义的全局header

例如：
```
  http-client:
    # ↱自定义全局http请求头: 为发出的所有数据包带上指定的自定义请求头
    headers: {
      test: test
    }
```

## 0.0.6(2023-07-16)

### 问题修复

修复list等命令报错的问题

## 0.0.5(2023-07-16)

### 新增特性

1. 新增对多种目标文件格式的支持：

   `-i` 参数支持自动识别文件类型并提取扫描目标

   - 支持 `CSV` / `Excel` 格式自动提取扫描目标
   - 支持将任意文件视为文本提取扫描目标
   - 支持对 `zip` 包中的文件自动识别扫描目标（识别其中可解析的 `文本/CSV/Excel` 格式）。

   例：
    ``` bash
    xpoc -i target.txt
    xpoc -i target.csv
    xpoc -i target.xlsx
    xpoc -i target.zip
    ```

2. 简化升级命令，执行  `xpoc up`  即可升级到最新版本，升级自身时也会同步更新插件。

3. 添加默认的扫描端口： `80,443,8080,8443,8888,8000,9090`

   输入目标为IP或域名且未指定扫描端口时会进行对这些端口的探测。
   （需删除当前配置文件，重新新生成配置后生效）默认端口可在配置文件中通过tcp_ports参数编辑。

4. 降低默认的指纹识别强度，加快扫描进度。

   （需删除当前配置文件，重新新生成配置后生效）默认的指纹识别强度可在配置文件中通过max_rarity参数编辑。

### 修复与优化

1. 优化策略文件加载，自定义策略自动继承全局命令行配置。

2. 修复目标文件中存在空行会解析错误的问题。

## 0.0.4(2023-05-30)

### 新增特性

1. 新增-v参数，可以打印当前情况下会执行的插件，可用于检查策略是否符合预期。

   例：`xpoc -v` 可打印默认策略下会执行的扫描插件。

2. 优化命令行体验，可在任意位置使用-h,-help参数打印帮助信息。

3. 优化命令行体验，目标可以在参数之前输入，多个目标可用空格或','进行分割。

   例：`xpoc testtest.fun -p 80,443`

### 修复与优化

- 优化升级检查功能
- 修复调度引擎中的问题
- 升级YAML PoC引擎

## 0.0.3(2023-05-25)

### 新增特性

简化默认参数和部分命令的使用

- `xpoc -t example.com` 可简化为 `xpoc example.com`
- `xpoc add -f ./myplugin.yaml` 可简化为 `xpoc add ./myplugin.yaml`
- `xpoc pull -id pluginID` 可简化为 `xpoc pull pluginID`

注意：需要删除当前配置文件，重新启动，自动生成新配置后方可生效

或在配置文件 “flag” 配置中，对希望缺省的参数值手工添加“,default”配置，如

```yaml
    util-target-split:
        required_targets: t,default
```

### Tips

- 默认的配置文件位于 `$HOME/.xray/xpoc-config.yaml`
- -c 参数用于指定特定的配置文件


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
