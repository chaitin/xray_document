# 指纹编写模板

## HTTP

[//]: # (TODO)

## TCP

通过建立连接，发送一些特定的数据等的方式，使用正则在响应中匹配特征来明确信息。

### 推荐写法

由于同一个服务可能存在多个版本，也就可能存在多个正则匹配语句，所以建议：
- 在set中提前将需要的内容写完
- 如果该脚本中的所有规则的逻辑都是或运算，且探测逻辑大致相似的话，使用payload进行编写
* 这样可以有效的减少脚本的体积，减少重复的逻辑，让看到脚本的人可以更加清晰的看出脚本的探测逻辑。

以下案例将均按照推荐写法进行编写

### 无输出信息

```yaml=
name: fingerprint-yaml-tcp-3cx_tunnel
manual: false
transport: tcp
set:
    re1: '"^\\x04\\0\\xfb\\xffLAPK"'
rules:
    r1:
        request:
            cache: true
            content: ""
            read_timeout: "6"
        expression: re1.bmatches(response.raw)
expression: r1()
detail:
    fingerprint:
        infos:
            - type: system_bin
              name: 3cx-tunnel
```

### 存在输出信息

```yaml=
name: fingerprint-yaml-tcp-openbsd-openssh
manual: false
transport: tcp
set:
    re1: string("^SSH-([\\d.]+)-OpenSSH_(?P<version0>[\\w._-]+)_Mikrotik_v(?P<version1>[\\d.]+)\\r?\\n")
rules:
    r1:
        request:
            cache: true
            content: ''
            read_timeout: '3'
        expression: re1.bmatches(response.raw)
        output:
            re1_result: re1.bsubmatch(response.raw)
            device: string("router")
            version: 're1_result["version0"] + " mikrotik " + re1_result["version1"]'
expression: r1()
detail:
    fingerprint:
        infos:
            - type: system_bin
              name: openssh
              version: '{{version}}'
              device: '{{device}}'
```

### 存在多个规则

```yaml=
name: fingerprint-yaml-tcp-mysql
manual: false
transport: tcp
set:
  GenericLines: b"\r\n\r\n"
payloads:
  payloads:
    a:
      re: '"^\\x10\\0\\0\\x01\\xff\\x13\\x04Bad handshake$"'
    b:
      re: '"(?s)^.\\0\\0\\0\\xff..Host .* is not allowed to connect to this MySQL server$"'
    c:
      re: '"(?s)^.\\0\\0\\0\\xff..Host .* is not allowed to connect to this MariaDB server$"'
    d:
      re: '"(?s)^.\\0\\0\\0\\xff..Too many connections"'
    e:
      re: '"(?s)^.\\0\\0\\0\\xff..Host .* is blocked because of many connection errors"'
    f:
      re: '"^.\\0\\0\\0\\xff..Le h\\xf4te ''[-.\\w]+'' n''est pas authoris\\xe9 \\xe0 se connecter \\xe0 ce serveur MySQL$"'
    g:
      re: '"(?s)^.\\0\\0\\0\\xff..Host hat keine Berechtigung, eine Verbindung zu diesem MySQL Server herzustellen\\."'
    h:
      re: '"(?s)^.\\0\\0\\0\\xff..Host ''[-\\w_.]+'' hat keine Berechtigung, sich mit diesem MySQL-Server zu verbinden"'
    i:
      re: '"(?s)^.\\0\\0\\0\\xff..Al sistema ''[-.\\w]+'' non e` consentita la connessione a questo server MySQL$"'
    j:
      re: '"(?s)^.\\0\\0\\0...Servidor ''[-.\\w]+'' est\\xe1 bloqueado por muchos errores de conexi\\xf3n\\.  Desbloquear con ''mysqladmin flush-hosts''"'
    k:
      re: '"^.\\0\\0\\0...''Host'' ''[-.\\w]+'' n\\xe3o tem permiss\\xe3o para se conectar com este servidor MySQL"'
    l:
      re: '"(?s)^.\\0\\0\\0\\xffj\\x04''[\\d.]+'' .* MySQL"'
    m:
      re: '"(?s)^.\\0\\0\\0\\x0a(?P<version>5\\.[-_~.+:\\w]+MariaDB-[-_~.+:\\w]+~bionic)\\0"'
    n:
      re: '"(?s)^.\\0\\0\\0\\x0a(?P<version>[\\w._-]+)\\0............\\0\\x5f\\xd3\\x2d\\x02\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0............\\0$"'
    o:
      re: '"(?s)^.\\0\\0\\0\\x0a(?P<version>[\\w._-]+)\\0............\\0\\x5f\\xd1\\x2d\\x02\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0............\\0$"'
    p:
      re: '"(?s)^.\\0\\0\\0\\x0a(?P<version>5\\.[-_~.+:\\w]+MariaDB-[-_~.+:\\w]+)\\0"'
    q:
      re: '"(?s)^.\\0\\0\\0.(?P<version>3\\.[-_~.+\\w]+)\\0.*\\x08\\x02\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0\\0$"'
    r:
      re: '"(?s)^.\\0\\0\\0\\x0a(?P<version>3\\.[-_~.+\\w]+)\\0...\\0"'
    s:
      re: '"(?s)^.\\0\\0\\0\\x0a(?P<version>4\\.[-_~.+\\w]+)\\0"'
    t:
      re: '"(?s)^.\\0\\0\\0\\x0a(?P<version>5\\.[-_~.+\\w]+)\\0"'
    u:
      re: '"(?s)^.\\0\\0\\0\\x0a(?P<version>6\\.[-_~.+\\w]+)\\0...\\0"'
    v:
      re: '"(?s)^.\\0\\0\\0\\x0a(?P<version>8\\.[-_~.+\\w]+)\\0...\\0"'
    w:
      re: '"(?s)^.\\0\\0\\0.(?P<version>[012]\\.[\\w.-]+)(?: \\([0-9a-f]+\\))?\\0"'
    x:
      re: '"^.\\0\\0\\0\\x0a(?P<version>0[\\w._-]+)\\0"'
rules:
  r1:
    request:
      cache: true
      content: '{{GenericLines}}'
    expression: re.bmatches(response.raw)
    output:
      result: re.bsubmatch(response.raw)
      osname: |
        re.contains("bionic") ? "Linux" : ""
      version: result["version"]
expression: r1()
detail:
  fingerprint:
    infos:
      - type: system_bin
        name: mysql
        version: '{{version}}'
      - type: operating_system
        name: '{{osname}}'
```
当在使用上述的格式的时候，不免会遇到一些需要进行判断的问题，比如上述的osname，只有到m规则时，是‘Linux’否则是空，在这个时候，我们就需要使用到三目运算符。

`condition ? true_value : false_value`

因为m规则中包含其他规则没有的`bionic`，所以使用这个进行判断，达到上述的效果。