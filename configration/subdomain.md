!> 注意，此功能只在高级版中提供

```yaml
subdomain:
  max_parallel: 30                      # 子域名探测的并发度
  allow_recursion: false                # 是否允许递归探测, 开启后，扫描完一级域名后，会自动将一级的每个域名作为新的目标
  max_recursion_depth: 3                # 最大允许的递归深度, 3 表示 3 级子域名 仅当 allow_recursion 开启时才有意义
  web_only: false                       # 结果中仅显示有 web 应用的, 没有 web 应用的将被丢弃
  ip_only: false                        # 结果中仅展示解析出 IP 的，没有解析成功的将被丢弃
  servers:                              # 子域名扫描过程中使用的 DNS Server
  - 8.8.8.8
  - 8.8.4.4
  - 223.5.5.5
  - 223.6.6.6
  - 114.114.114.114
  sources:
    brute:
      enabled: true
      main_dict: ""                     # 一级大字典路径，为空将使用内置的 TOP 30000 字典
      sub_dict: ""                      # 其他级小字典路径，为空将使用内置过的 TOP 100 字典
    httpfinder:
      enabled: true                     # 使用 http 的一些方式来抓取子域名，包括 js, 配置文件，http header 等等
    dnsfinder:
      enabled: true                     # 使用 dns 的一些错误配置来找寻子域名，如区域传送（zone transfer)
    certspotter:                        # 下面的通过 api 获取的了
      enabled: true
    crt:
      enabled: true
    hackertarget:
      enabled: true
    qianxun:
      enabled: true
    rapiddns:
      enabled: true
    sublist3r:
      enabled: true
    threatminer:
      enabled: true
    virusTotal:
      enabled: true

```

子域名的配置项相对比较简洁，对照注释大都可以理解。