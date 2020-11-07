基础爬虫的配置项对应于 `basic-crawler` 部分，默认的配置如下，用法参照文件中的注释:

```yaml
basic-crawler:
  max_depth: 0                          # 最大爬取深度， 0 为无限制
  max_count_of_links: 0                 # 本次爬取收集的最大链接数, 0 为无限制
  allow_visit_parent_path: false        # 是否允许爬取父目录, 如果扫描目标为 t.com/a/且该项为 false, 那么就不会爬取 t.com/ 这级的内容
  restriction:                          # 爬虫的允许爬取的资源限制, 为空表示不限制。爬虫会自动添加扫描目标到 Hostname_allowed。
    hostname_allowed: []                # 允许访问的 Hostname，支持格式如 t.com、*.t.com、1.1.1.1、1.1.1.1/24、1.1-4.1.1-8
    hostname_disallowed:                # 不允许访问的 Hostname，支持格式如 t.com、*.t.com、1.1.1.1、1.1.1.1/24、1.1-4.1.1-8
    - '*google*'
    - '*github*'
    - '*.gov.cn'
    - '*chaitin*'
    - '*.xray.cool'
    port_allowed: []                    # 允许访问的端口, 支持的格式如: 80、80-85
    port_disallowed: []                 # 不允许访问的端口, 支持的格式如: 80、80-85
    path_allowed: []                    # 允许访问的路径，支持的格式如: test、*test*
    path_disallowed: []                 # 不允许访问的路径, 支持的格式如: test、*test*
    query_key_allowed: []               # 允许访问的 Query Key，支持的格式如: test、*test*
    query_key_disallowed: []            # 不允许访问的 Query Key, 支持的格式如: test、*test*
    fragment_allowed: []                # 允许访问的 Fragment, 支持的格式如: test、*test*
    fragment_disallowed: []             # 不允许访问的 Fragment, 支持的格式如: test、*test*
    post_key_allowed: []                # 允许访问的 Post Body 中的参数, 支持的格式如: test、*test*
    post_key_disallowed: []             # 不允许访问的 Post Body 中的参数, 支持的格式如: test、*test*
  basic_auth:                           # 基础认证信息
    username: ""
    password: ""
```
