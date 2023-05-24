# xpoc change log

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

### 2. yaml插件能力更新

1. 新增get404Path函数，详情请见[函数介绍](guide/poc/example/http/get404Path.md)
