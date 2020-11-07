# 子域名的消息格式

```json
{
  "type": "subdomain",
  "data": {
    "cname": [],
    "domain": "example.com",
    "extra": "",
    "ip": [
      {
        "asn": "EDGECAST (ASN15133)",
        "country": "北美洲 美国",
        "ip": "93.184.216.34"
      },
      {
        "asn": "EDGECAST (ASN15133)",
        "country": "北美洲 美国",
        "ip": "2606:2800:220:1:248:1893:25c8:1946"
      }
    ],
    "parent": "example.com",
    "verbose_name": "initial",
    "web": [
      {
        "link": "http://example.com/",
        "server": "ECS (sjc/4FB8)",
        "status": 200,
        "title": "Example Domain"
      },
      {
        "link": "https://example.com/",
        "server": "ECS (sjc/4E8D)",
        "status": 200,
        "title": "Example Domain"
      }
    ]
  }
}
```