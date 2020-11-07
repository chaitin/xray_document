# 漏洞扫描时的消息格式

## 漏洞信息

```json
{
  "type": "web_vuln",
  "data": {
    "create_time": 1604736253090,
    "detail": {
      "addr": "http://127.0.0.1:9000/xss/example1.php?name=hacker",
      "extra": {
        "param": {
          "key": "name",
          "position": "query",
          "value": "pkbnekwkjhwzabxnfjwh"
        }
      },
      "payload": "<sCrIpT>alert(1)</ScRiPt>",
      "snapshot": [
        [
          "GET /xxx",
          "HTTP/1.1 200 OK"
        ]
      ]
    },
    "plugin": "xss/reflected/default",
    "target": {
      "params": [],
      "url": "http://127.0.0.1:9000/xss/example1.php"
    }
  }
}
```

+ type 是 `web_vuln` 或 `host_vuln`
+ snapshot 是漏洞探测的请求流，定义是 `[][2]string`, 即每一组有两部分，对应于请求和响应。对于 sql 注入这种就会有多组请求响应。
+ target 这一部分实际意义不大, 主要是 detail 中的部分, detail 的定义为:

```go
type VulnDetail struct {
	Addr     string                 `json:"addr" yaml:"addr"`
	Payload  string                 `json:"payload" yaml:"payload"`
	SnapShot []interface{}          `json:"snapshot" yaml:"snapshot"`
	Extra    map[string]interface{} `json:"extra" yaml:"extra"`
}
```

## web 统计信息

```json
{
  "type": "web_statistic",
  "data": {
    "num_found_urls": 1,
    "num_scanned_urls": 1,
    "num_sent_http_requests": 3,
    "average_response_time": 0,
    "ratio_failed_http_requests": 0,
  }
}
```
+ `num_found_urls` 发现的扫描目标数量
+ `num_scanned_urls` 已扫描完成的扫描目标数量
+ `num_sent_http_requests` 已经发出的漏洞探测请求数量
+ `average_response_time` 近 30s 请求平均响应时间
+ `ratio_failed_http_requests` 近 30s 请求失败率

这些信息除了表面意思，还有其他隐藏含义：

+ pending 的数量等于 `num_found_urls` - `num_scanned_urls`, 如果 pending = 0，那么就意味着扫描完成了
+ `average_response_time` 比较高 (>200ms) 意味着网络延迟比较大
+ `ratio_failed_http_requests` 比较高(>5%) 意味着可能有 waf, 反正就是很多请求失败了