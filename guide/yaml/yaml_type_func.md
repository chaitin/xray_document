# YAML插件的参数

## 类型
xray 支持所有CEL文档中的类型，同时还注入了几种特殊的类型，包含：

- `addrType` 连接地址信息
- `connInfoType` 连接信息，包含源地址和目的地址, 可以通过 `response.conn`
- `urlType` url 类型，可以 `request.url`、`response.url` 和 `reverse.url` 调用
- `reverseType` 反连平台类型
- `request` 扫描请求
- `response` 请求的响应，通用属性包含：`raw`

其中注入的 request 和 response 类型是随着 `transport` 对应的值进行改变的。

### 基本类型

#### addrType

addrType 类型包含字段如下, 设变量名为 `addr`

| 变量名              | 类型       | 说明                                                        | 适用版本         |
|------------------|----------|-----------------------------------------------------------|--------------|
| `addr.transport` | `string` | tranport                                                  | xray ≥ 1.8.4 |
| `addr.addr`      | `string` | 目的地址， 获取失败时返回空字符串，形如： `192.0.2.1:25`, `[2001:2001::1]:80` | xray ≥ 1.8.4 |
| `addr.port`      | `string` | 端口号， 获取失败时返回 `""`                                         | xray ≥ 1.8.4 |

#### connInfoType

connInfoType 类型包含字段如下, 设变量名为 `conn`

| 变量名                | 类型         | 说明     | 适用版本         |
|--------------------|------------|--------|--------------|
| `conn.source`      | `addrType` | 源地址信息  | xray ≥ 1.8.4 |
| `conn.destination` | `addrType` | 目的地址信息 | xray ≥ 1.8.4 |

#### urlType

urlType 类型包含的字段如下, 设变量名为 `url`, 以 `http://example.com:8080/a?c=d#x=y` 为例:

| 变量名            | 类型       | 说明                                 | 适用版本         |
|----------------|----------|------------------------------------|--------------|
| `url.scheme`   | `string` | url 的 scheme, 示例为 `"http"`         | xray ≥ 1.8.4 |
| `url.domain`   | `string` | url 的域名，示例例为 `"example.com"`       | xray ≥ 1.8.4 |
| `url.host`     | `string` | url 的主机名，示例为 `"example.com:8080"`  | xray ≥ 1.8.4 |
| `url.port`     | `string` | url 的 port，注意这里也是字符串。 示例为 `"8080"` | xray ≥ 1.8.4 |
| `url.path`     | `string` | url 的 path， 示例为 `"/a"`             | xray ≥ 1.8.4 |
| `url.query`    | `string` | url 的 query, 示例为 `"c=d"`           | xray ≥ 1.8.4 |
| `url.fragment` | `string` | url 的锚点，示例为 `"x=y"`                | xray ≥ 1.8.4 |

#### reverseType

reverseType 类型包含字段如下, 设变量名为 `reverse`（需要先使用 `newReverse()` 生成实例)

| 变量名                             | 类型                        | 说明                            | 适用版本         |
|---------------------------------|---------------------------|-------------------------------|--------------|
| `reverse.url`                   | `urlType`                 | 反连平台的 url                     | xray ≥ 1.8.4 |
| `reverse.domain`                | `string`                  | 反连平台的域名                       | xray ≥ 1.8.4 |
| `reverse.rmi`                   | `urlType`                 | 反连平台的rmi协议url                 | xray ≥ 1.9.4 |
| `reverse.ip`                    | `string`                  | 反连平台的 ip 地址                   | xray ≥ 1.8.4 |
| `reverse.is_domain_name_server` | `bool`                    | 反连平台的 domain 是否同时是 nameserver | xray ≥ 1.8.4 |
| `reverse.wait(timeout)`         | `func (timeout int) bool` | 等待 timeout 秒，并返回是否在改时间内获得了信息  | xray ≥ 1.8.4 |

> 参数详情介绍：[🔎详情](guide/poc/exampleType/reverse.md)

#### Timestamp

Timestamp 类型实际是google.protobuf.Timestamp类型，为cel表达式本身自带的类型，其本身包含了非常多的方法，详情可查看：[Timestamp](guide/poc/exampleType/timestamp.md)

### tcp request and response

其中 request 包含的字段如下：

| 变量名           | 类型       | 说明   | 适用版本         |
|---------------|----------|------|--------------|
| `request.raw` | `[]byte` | 原始请求 | xray ≥ 1.8.4 |

response 包含的字段如下:

| 变量名             | 类型             | 说明     | 适用版本         |
|-----------------|----------------|--------|--------------|
| `response.conn` | `connInfoType` | 连接相关信息 | xray ≥ 1.8.4 |
| `response.raw`  | `[]byte`       | 原始响应   | xray ≥ 1.8.4 |

### udp request and response

其中 request 包含的字段如下：

| 变量名           | 类型       | 说明   | 适用版本         |
|---------------|----------|------|--------------|
| `request.raw` | `[]byte` | 原始请求 | xray ≥ 1.8.4 |

response 包含的字段如下:

| 变量名             | 类型             | 说明     | 适用版本         |
|-----------------|----------------|--------|--------------|
| `response.conn` | `connInfoType` | 连接相关信息 | xray ≥ 1.8.4 |
| `response.raw`  | `[]byte`       | 原始响应   | xray ≥ 1.8.4 |

### http request and response

其中 request 包含的字段如下：

| 变量名                    | 类型                  | 说明                                                                                                                                      | 适用版本         |
|------------------------|---------------------|-----------------------------------------------------------------------------------------------------------------------------------------|--------------|
| `request.raw`          | `[]byte`            | 原始请求                                                                                                                                    | xray ≥ 1.8.4 |
| `request.url`          | `urlType`           | 自定义类型 urlType, 请查看下方 urlType 的说明                                                                                                        | xray ≥ 1.8.4 |
| `request.method`       | `string`            | 原始请求的方法                                                                                                                                 | xray ≥ 1.8.4 |
| `request.headers`      | `map[string]string` | 原始请求的HTTP头，是一个键值对（均为小写），我们可以通过`headers['server']`来获取值。如果键不存在，则获取到的值是空字符串。注意，该空字符串不能用于 `==` 以外的操作，否则不存在的时候将报错，需要先 `in` 判断下。详情参考下文常用函数章节。 | xray ≥ 1.8.4 |
| `request.content_type` | `string`            | 原始请求的 content-type 头的值, 等于`request.headers["Content-Type"]`                                                                             | xray ≥ 1.8.4 |
| `request.raw_header`   | `[]byte`            | 原始的 header 部分，需要使用字节流相关方法来判断。                                                                                                           | xray ≥ 1.8.4 |
| `request.body`         | `[]byte`            | 原始请求的 body，需要使用字节流相关方法来判断。如果是 GET， body 为空。                                                                                             | xray ≥ 1.8.4 |

response 包含的字段如下:

| 变量名                     | 类型                  | 说明                                                 | 适用版本         |
|-------------------------|---------------------|----------------------------------------------------|--------------|
| `response.raw`          | `[]byte`            | 原始响应                                               | xray ≥ 1.8.4 |
| `response.url`          | `urlType`           | 自定义类型 urlType, 请查看下方 urlType 的说明                   | xray ≥ 1.8.4 |
| `response.status`       | `int`               | 返回包的status code                                    | xray ≥ 1.8.4 |
| `response.raw_header`   | `[]byte`            | 原始的 header 部分，需要使用字节流相关方法来判断。                      | xray ≥ 1.8.4 |
| `response.body`         | `[]byte`            | 返回包的Body，因为是一个字节流（bytes）而非字符串，后面判断的时候需要使用字节流相关的方法  | xray ≥ 1.8.4 |
| `response.body_string`  | `string`            | 返回包的Body，是一个字符串                                    | xray ≥ 1.9.1 |
| `response.headers`      | `map[string]string` | 返回包的HTTP头，类似 `request.headers`。                    | xray ≥ 1.8.4 |
| `response.content_type` | `string`            | 返回包的content-type头的值                                | xray ≥ 1.8.4 |
| `response.latency`      | `int`               | 响应的延迟时间，可以用于 sql 时间盲注的判断，单位毫秒 (ms)                 | xray ≥ 1.8.4 |
| `response.title`        | `[]byte`            | 返回包的Title，因为是一个字节流（bytes）而非字符串，后面判断的时候需要使用字节流相关的方法 | xray ≥ 1.8.5 |
| `response.title_string` | `string`            | 返回包的Title，是一个字符串                                   | xray ≥ 1.8.5 |

## 函数

xray 支持所有 CEL 文档中的函数，同时还新增了一些函数支持，下面列举一下常用的函数：

**注**：gocel 原生的正则支持使用的 golang 自带的 regex 库， 在脚本中我们使用 [regex2](github.com/dlclark/regexp2) 替换了自带的 regex 库

## rule 函数

**注**：只有脚本层级的 expression 才能使用这些函数

| 函数名         | 函数原型                      | 说明                         | 适用版本         |
|-------------|---------------------------|----------------------------|--------------|
| `rule name` | `func <rule name>() bool` | 返回这条 rule expression 执行的结果 | xray ≥ 1.8.4 |

## 字符串处理

| 函数名            | 函数原型                                                     | 说明                                                                | 适用版本         | 详情                                                                                              |
|----------------|----------------------------------------------------------|-------------------------------------------------------------------|--------------|-------------------------------------------------------------------------------------------------|
| `contains`     | `func (s1 string) contains(s2 string) bool`              | 判断 s1 是否包含 s2，返回 bool 类型结果                                        | xray ≥ 1.8.4 | [🔎](guide/poc/example/string/contains.md)                                                      |
| `icontains`    | `func (s1 string) icontains(s2 string) bool`             | 判断 s1 是否包含 s2，返回 bool 类型结果, 与 contains 不同的是，icontains 忽略大小写       | xray ≥ 1.8.4 | [🔎](guide/poc/example/string/icontains.md)                                                     |
| `substr`       | `func substr(string, start int, length int) string`      | 截取字符串                                                             | xray ≥ 1.8.4 | [🔎](guide/poc/example/string/substr.md)                                                        |
| `replaceAll`   | `func replaceAll(string, old string, new string) string` | 将 string 中的 old 替换为 new，返回替换后的 string                             | xray ≥ 1.8.4 | [🔎](guide/poc/example/string/replaceAll.md)                                                    |
| `printable`    | `func printable(string) string`                          | 将 string 中的非 unicode 编码字符去掉                                       | xray ≥ 1.8.4 | [🔎](guide/poc/example/string/printable.md)                                                     |
| `toUintString` | `func toUintString(s1 string, direction string) string`  | direction 取值为 `>`,`<`表示读取方向, 将 s1 按 direction 读取为一个整数，返回该整数的字符串形式 | xray ≥ 1.8.4 | [🔎](guide/poc/example/string/toUintString.md)                                                  |
| `startsWith`   | `func (s1 string) startsWith(s2 string) bool`            | 判断 s1 是否由 s2 开头                                                   | 全版本          | [🔎](https://github.com/google/cel-spec/blob/master/tests/simple/testdata/string.textproto#L42) |
| `endsWith`     | `func (s1 string) endsWith(s2 string) bool`              | 判断 s1 是否由 s2 结尾                                                   | 全版本          | [🔎](https://github.com/google/cel-spec/blob/master/tests/simple/testdata/string.textproto#L76) |
| `basename`     | `func basename(s1 string) string`                        | 返回 URL 的最后一个路径的名称                                                 | xray ≥ 1.9.1 | [🔎](guide/poc/example/string/basename.md)                                                      |
| `dir`          | `func dir(s1 string) string`                             | 返回 URL 的路径                                                        | xray ≥ 1.9.1 | [🔎](guide/poc/example/string/dir.md)                                                           |
| `upper`        | `func upper(s1 string) string`                           | 将 string 中的小写字母转换成大写                                              | xray ≥ 1.9.3 | [🔎](guide/poc/example/string/upper.md)                                                         |
| `rev`          | `func rev(s1 string) string`                             | 将 string 反向输出，主要用于验证命令执行                                          | xray ≥ 1.9.3 | [🔎](guide/poc/example/string/rev.md)                                                           |
| `size`         | `func size(s1 string) int`                               | 返回 string 的长度                                                     | 全版本          | [🔎](https://github.com/google/cel-spec/blob/master/tests/simple/testdata/string.textproto#L3)  |

## []byte 处理

| 函数名           | 函数原型                                                    | 说明                                                                            | 适用版本         | 详情                                                                                              |
|---------------|---------------------------------------------------------|-------------------------------------------------------------------------------|--------------|-------------------------------------------------------------------------------------------------|
| `bcontains`   | `func (b1 bytes) bcontains(b2 bytes) bool`              | 判断一个 b1 是否包含 b2，返回 bool 类型结果。与 contains 不同的是，bcontains 是字节流（bytes）的查找         | xray ≥ 1.8.4 | [🔎](guide/poc/example/bytes/bcontains.md)                                                      |
| `ibcontains`  | `func (b1 bytes) ibcontains(b2 bytes) bool`             | 判断 b1 是否包含 b2，返回 bool 类型结果, 与 contains 不同的是，ibcontains 是字节流（bytes）的查找, 且忽略大小写 | xray ≥ 1.8.4 | [🔎](guide/poc/example/bytes/ibcontains.md)                                                     |
| `bstartsWith` | `func (b1 bytes) bstartsWith(b2 bytes) bool`            | 判断一个 b1 是否由 b2 开头，返回 bool 类型结果。与 startsWith 不同的是，bcontains 是字节流（bytes）的查找     | xray ≥ 1.8.4 | [🔎](guide/poc/example/bytes/bstartWith.md)                                                     |
| `bformat`     | `func (b1 bytes) bformat(int, int, string, int) string` | 将 bytes 进行进制转换编码，可以根据输入的参数转换成对应的进制编码，并可以自定义转换格式                               | xray ≥ 1.8.5 | [🔎](guide/poc/example/bytes/bformat.md)                                                        |
| `size`        | `func size(b1 bytes) int`                               | 返回 bytes 的长度                                                                  | 全版本          | [🔎](https://github.com/google/cel-spec/blob/master/tests/simple/testdata/string.textproto#L31) |

## 编码函数

| 函数名            | 函数原型                                                 | 说明                                                                                 | 适用版本         | 详情                                             |
|----------------|------------------------------------------------------|------------------------------------------------------------------------------------|--------------|------------------------------------------------|
| `base64`       | `func base64(v1 string/bytes) string`                | 将字符串或 bytes 进行 base64 编码                                                           | xray ≥ 1.8.4 | [🔎](guide/poc/example/encode/base64.md)       |
| `base64Decode` | `func base64Decode(v1 string/bytes) string`          | 将字符串或 bytes 进行 base64 解码                                                           | xray ≥ 1.8.4 | [🔎](guide/poc/example/encode/base64Decode.md) |
| `urlencode`    | `func urlencode(v1 string/bytes) string`             | 将字符串或 bytes 进行 urlencode 编码                                                        | xray ≥ 1.8.4 | [🔎](guide/poc/example/encode/urlencode.md)    |
| `urlencodeall` | `func urlencodeall(v1 string/bytes) string`          | 将字符串或 bytes 进行 urlencode 编码, 结果为全字符编码                                              | xray ≥ 1.8.5 | [🔎](guide/poc/example/encode/urlencodeall.md) |
| `urldecode`    | `func urldecode(v1 string/bytes) string`             | 将字符串或 bytes 进行 urldecode 解码                                                        | xray ≥ 1.8.4 | [🔎](guide/poc/example/encode/urldecode.md)    |
| `faviconHash`  | `func faviconHash(v1 string/bytes) int`              | 将字符串或 bytes 进行 faviconHash 编码，参考：[iconhash](https://github.com/Becivells/iconhash) | xray ≥ 1.8.4 | [🔎](guide/poc/example/encode/faviconHash.md)  |
| `htmlEscape`   | `func htmlEscape(string/bytes, string, bool) string` | 将字符串或 bytes 进行 htmlEscape 编码，支持 named/numeric/hex 模式，支持全/非全字符编码                    | xray ≥ 1.9.4 | [🔎](guide/poc/example/encode/htmlEscape.md)   |
| `htmlUnescape` | `func htmlUnescape(string/bytes) string`             | 将字符串或 bytes 进行 htmlEscape 解码，支持 named/numeric/hex 模式，支持全/非全字符编码                    | xray ≥ 1.9.4 | [🔎](guide/poc/example/encode/htmlUnescape.md) |
| `hex`          | `func hex(string/bytes) string`                      | 将字符串或 bytes 进行 hex 编码                                                              | xray ≥ 1.9.4 | [🔎](guide/poc/example/encode/hex.md)          |
| `hexDecode`    | `func hexDecode(string/bytes) string`                | 将字符串或 bytes 进行 hex 解码                                                              | xray ≥ 1.9.4 | [🔎](guide/poc/example/encode/hexDecode.md)    |

## 加密函数

| 函数名                  | 函数原型                                                                 | 说明                                   | 适用版本         | 详情                                                |
|----------------------|----------------------------------------------------------------------|--------------------------------------|--------------|---------------------------------------------------|
| `md5`                | `func md5(v1 string/bytes) string`                                   | 字符串的 md5                             | xray ≥ 1.8.4 | [🔎](guide/poc/example/encryption/md5.md)         |
| `sha`                | `func sha(v1 string/bytes, s1 string)string`                         | 该函数可以将指定字符串或 bytes 进行 sha 系列计算。      | xray ≥ 1.8.4 | [🔎](guide/poc/example/encryption/sha.md)         |
| `hmacSha`            | `func hmacSha(v1 string/bytes, s1 string, s2 string)string`          | 该函数可以将指定字符串或 bytes 进行 hmac_sha 系列计算。 | xray ≥ 1.8.4 | [🔎](guide/poc/example/encryption/hmacSha.md)     |
| `rsaEncryptPKCS1v15` | `func rsaEncryptPKCS1v15(b1 bytes, k1 string)bytes`                  | 该函数可以将指定数据按照PKCS1v15的填充模式进行RSA加密运算   | xpoc ≥ 0.0.8 | [🔎](guide/poc/example/encryption/rsaPKCS1v15.md) |
| `rsaDecryptPKCS1v15` | `func rsaDecryptPKCS1v15(b1 bytes, k1 string)bytes`                  | 该函数可以将指定数据按照PKCS1v15的填充模式进行RSA解密运算   | xpoc ≥ 0.0.8 | [🔎](guide/poc/example/encryption/rsaPKCS1v15.md) |
| `aesDecrypt`         | `func aesDecrypt(d1 bytes/string, k1 bytes/string, m1 string)string` | 该函数可以对aes加密的数据进行解密                   | xpoc ≥ 0.0.8 | [🔎](guide/poc/example/encryption/aes.md)         |

## 随机值

| 函数名               | 函数原型                                    | 说明                | 适用版本         | 详情                                                |
|-------------------|-----------------------------------------|-------------------|--------------|---------------------------------------------------|
| `randomInt`       | `func randomInt(from, to int) int`      | 两个范围内的随机数         | xray ≥ 1.8.4 | [🔎](guide/poc/example/random/randomInt.md)       |
| `randomLowercase` | `func randomLowercase(n length) string` | 指定长度的小写字母组成的随机字符串 | xray ≥ 1.8.4 | [🔎](guide/poc/example/random/randomLowercase.md) |

## 正则

| 函数名         | 函数原型                                                     | 说明                                                                                                                     | 适用版本         | 详情                                         |
|-------------|----------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|--------------|--------------------------------------------|
| `matches`   | `func (s1 string) matches(s2 string) bool`               | 使用正则表达式 s1 来匹配 s2，返回 bool 类型匹配结果                                                                                       | xray ≥ 1.8.4 | [🔎](guide/poc/example/regex/matches.md)   |
| `submatch`  | `func (s1 string) submatch(s2 string) map[string]string` | 使用正则表达式 s1 来匹配 s2，返回 map[string]string 类型结果，**注**：只返回具名的正则匹配结果 (?P<name>…) 格式                                          | xray ≥ 1.8.4 | [🔎](guide/poc/example/regex/submatch.md)  |
| `bmatches`  | `func (s1 string) bmatches(b1 bytes) bool`               | 使用正则表达式 s1 来匹配 b1，返回 bool 类型匹配结果。与 matches 不同的是，bmatches 匹配的是字节流（bytes）                                                | xray ≥ 1.8.4 | [🔎](guide/poc/example/regex/bmatches.md)  |
| `bsubmatch` | `func (s1 string) bsubmatch(b1 bytes) map[string]string` | 使用正则表达式 s1 来匹配 b1，返回 map[string]string 类型结果 **注**：只返回具名的正则匹配结果 (?P<name>…) 格式。与 submatch 不同的是，bsubmatch 匹配的是字节流（bytes） | xray ≥ 1.8.4 | [🔎](guide/poc/example/regex/bsubmatch.md) |

## 反连平台

| 函数名          | 函数原型                                                | 说明                           | 适用版本         | 详情 |
|--------------|-----------------------------------------------------|------------------------------|--------------|--|
| `newReverse` | `func newReverse() reverseType`                     | 返回一个 reverse 实例              | xray ≥ 1.8.4 | [🔎](guide/poc/exampleType/reverse.md) |
| `wait`       | `func (reverse reverseType) wait(timeout int) bool` | 等待 timeout 秒，并返回是否在该时间内获得了信息 | xray ≥ 1.8.4 | [🔎](guide/poc/example/reverse/reverse.md) |

## 时间

| 函数名           | 函数原型                                   | 说明                                      | 适用版本         |                                             |
|---------------|----------------------------------------|-----------------------------------------|--------------|---------------------------------------------|
| `now`         | `func now() Timestamp`                 | 使用该函数将返回当前时间，使用 int(now())，将会获取当前时间的时间戳 | xray ≥ 1.8.5 | [🔎](guide/poc/example/time/now.md)         |
| `sleep`       | `func sleep(i1 int) bool`              | 暂停执行等待指定的秒数                             | xray ≥ 1.8.4 | [🔎](guide/poc/example/time/sleep.md)       |
| `timeConvert` | `func timeConvert(int, string) string` | 该函数可以将指定时间戳转换成自定义格式的字符串                 | xray ≥ 1.8.5 | [🔎](guide/poc/example/time/timeConvert.md) |

## 版本

| 函数名              | 函数原型                                              | 说明                                                          | 适用版本         | 详情                                                |
|------------------|---------------------------------------------------|-------------------------------------------------------------|--------------|---------------------------------------------------|
| `versionIn`      | `func versionIn(s1 string,s2 string) bool`        | 判断所给出的版本是否在所限定的版本范围之内，版本格式参考: [semver](https://semver.org/) | xray ≥ 1.9.4 | [🔎](guide/poc/example/version/versionIn.md)      |
| `versionLess`    | `func (s1 string) versionLess(s2 string) bool`    | 判断所给出的版本是否小于所给出的版本，版本格式参考: [semver](https://semver.org/)    | xray ≥ 1.9.4 | [🔎](guide/poc/example/version/versionLess.md)    |
| `versionGreater` | `func (s1 string) versionGreater(s2 string) bool` | 判断所给出的版本是否大于所给出的版本，版本格式参考: [semver](https://semver.org/)    | xray ≥ 1.9.4 | [🔎](guide/poc/example/version/versionGreater.md) |
| `versionEqual`   | `func (s1 string) versionEqual(s2 string) bool`   | 判断所给出的版本是否等于所给出的版本，版本格式参考: [semver](https://semver.org/)    | xray ≥ 1.9.4 | [🔎](guide/poc/example/version/versionEqual.md)   |

## java 反序列化

| 函数名          | 函数原型                                                                | 说明                                                                                                        | 适用版本         | 详情                                              |
|--------------|---------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|--------------|-------------------------------------------------|
| `javaGadget` | `func javaGadget(gadget string, payload string, type string) bytes` | 依据所给出的生成链条以及 payload 内容生成对应的 gadget 内容，第一个参数接收 gadget 类型；第二个参数为 payload 内容，为 cmd/反连 URL；第三个参数为 payload 类型 | xray ≥ 1.9.4 | [🔎](guide/poc/example/ysoGadget/javaGadget.md) |

## 其它

| 函数名              | 函数原型                                        | 说明                                                      | 适用版本          | 详情                                              |
|------------------|---------------------------------------------|---------------------------------------------------------|---------------|-------------------------------------------------|
| `in`             | `string in map`                             | map 中是否包含某个 key                                         | xray ≥ 1.8.4  |                                                 |
| `getIconContent` | `func (r1 response) getIconContent() bytes` | 获取response中的icon的字节流数据，如果只存储了icon的地址，则会自动访问该icon，并返回字节流 | xray ≥ 1.9.3  | [🔎](guide/poc/example/other/getIconContent.md) |
| `get404Path`     | `func get404Path() string`                  | 获取一个长度为8的随机字符串，当作404页面的路径,并保证在一次扫描中，同一个目标将只有一个404path   | xpoc >= 0.0.1 | [🔎](guide/poc/example/http/get404path.md)      |
| `zipSlip`        | `func zipSlip(s1 string, b1 bytes) bytes`   | 该函数可以自定义生成一个zip文件                                       | xpoc >= 0.0.8 | [🔎](guide/poc/example/other/zipSlip.md)        |
