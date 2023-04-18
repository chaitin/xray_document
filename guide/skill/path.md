# 扫描路径 - path

1. 如果 path 是以 `/` 开头的， 取 dir 路径拼接
2. 如果 path 是以 `^` 开头的， uri 直接取该路径
3. 支持变量渲染

``` yaml
# target: http://example.com:8080/test/test
name: poc-yaml-http-path-test
manual: false
transport: http
set:
    # /test/test
    inputPath: request.url.path
rules:
    r0:
        request:
            cache: true
            method: GET
            # target: http://example.com:8080/test/a
            # 如果以 / 开头，取的传入 url 的 dir path 拼接的（考虑到 /admin 是一个单独站点这种情况）
            path: /a
        expression: "true"
        output:
            r0Url: request.url.path
    r1:
        request:
            cache: true
            method: GET
            # target: http://example.com:8080/test/test/b
            # 如果以 ^ 开头，取 path 作为请求路径
            path: '^{{inputPath}}/b'
        expression: "true"
    r2:
        request:
            cache: true
            method: GET
            # target: http://example.com:8080/c
            # 如果以 ^ 开头，取 path 作为请求路径
            path: ^/c
        expression: "true"
    r3:
        request:
            cache: true
            method: GET
            # target: http://example.com:8080/test/a/d
            # 如果不以 / 开头，取 path 作为请求路径
            path: '^{{r0Url}}/d'
        expression: "true"
expression: r0() && r1() && r2() && r3()
detail:
    author: yywing
```