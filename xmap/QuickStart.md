# 快速开始

xmap 为单文件二进制文件，无依赖，也无需安装，下载后直接使用。

!> 请注意，xmap为命令行工具，请不要双击运行，需要在命令行中运行。

## 下载地址

请下载的时候选择最新的版本下载。

+ Github: https://github.com/chaitin/xmap/releases （国外速度快）
+ CT stack: https://stack.chaitin.com/tool/detail?id=xxxx （国内速度快）

xmap 跨平台支持，请下载时选择需要的版本下载。

## 扫描

![](../assets/eme.gif)

1. 拉取所有云端指纹并扫描指定目标

    - `./xmap -t https://example.com -o result.html`

2. 查看所有云端的指纹

    - `./xmap list -a`

3. 批量扫描

    - `./xmap < targets.txt`
    - ` cat targets.txt | ./xmap`
    - `./xmap -i targets.txt`
