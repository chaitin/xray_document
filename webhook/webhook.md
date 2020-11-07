Webhook 是一种消息发送方式，表示如果发生了某个事件就往特定地址发送特定请求。借助这个特性可以实现诸如：

+ 对接微信/钉钉实现发现漏洞自动告警
+ 自动化的扫描统计与控制
+ 扫描状态批量管理
+ ...

xray 在下面的三个命令中定义了 webhook (`--webhook-output`), 点击左侧菜单查看详情。

其中统一的格式为:
```
{
    "type": "xxx",
    "data": {}
}
```

type 的 enum 为 ：

+ "web_statistic" web 扫描统计信息
+ "web_vuln" web 漏洞
+ "host_vuln" 服务漏洞
+ "subdomain" 子域名


<!-- 在 https://github.com/chaitin/xray/blob/master/webhook/ 有 xray 官方提供的 api 处理框架，请参照代码和注释，不再单独使用文档说明。

 - `app.py` 处理原始 json 转换为相关的 model
 - `model/vuln.py` web 漏洞、服务漏洞和统计信息的 model 定义 -->