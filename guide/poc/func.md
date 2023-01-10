# xray 脚本注入的函数

xray 支持所有 CEL 文档中的函数，同时还新增了一些函数支持，下面列举一下常用的函数：

**注**：gocel 原生的正则支持使用的 golang 自带的 regex 库， 在脚本中我们使用 [regex2](github.com/dlclark/regexp2) 替换了自带的 regex 库

## rule 函数

**注**：只有脚本层级的 expression 才能使用这些函数

| 函数名         | 函数原型                      | 说明                         | 适用版本         |
|-------------|---------------------------|----------------------------|--------------|
| `rule name` | `func <rule name>() bool` | 返回这条 rule expression 执行的结果 | xray ≥ 1.8.4 |

## 字符串处理

| 函数名            | 函数原型                                                     | 说明                                                                | 适用版本         | 详情                                          |
|----------------|----------------------------------------------------------|-------------------------------------------------------------------|--------------|---------------------------------------------|
| `contains`     | `func (s1 string) contains(s2 string) bool`              | 判断 s1 是否包含 s2，返回 bool 类型结果                                        | xray ≥ 1.8.4 | [🔎](guide/poc/example/string/contains.md)  |
| `icontains`    | `func (s1 string) icontains(s2 string) bool`             | 判断 s1 是否包含 s2，返回 bool 类型结果, 与 contains 不同的是，icontains 忽略大小写       | xray ≥ 1.8.4 | [🔎](guide/poc/example/string/icontains.md) |
| `substr`       | `func substr(string, start int, length int) string`      | 截取字符串                                                             | xray ≥ 1.8.4 | [🔎](guide/poc/example/string/substr.md)    |
| `replaceAll`   | `func replaceAll(string, old string, new string) string` | 将 string 中的 old 替换为 new，返回替换后的 string                             | xray ≥ 1.8.4 |                                             |
| `printable`    | `func printable(string) string`                          | 将 string 中的非 unicode 编码字符去掉                                       | xray ≥ 1.8.4 |                                             |
| `toUintString` | `func toUintString(s1 string, direction string) string`  | direction 取值为 `>`,`<`表示读取方向, 将 s1 按 direction 读取为一个整数，返回该整数的字符串形式 | xray ≥ 1.8.4 |                                             |
| `startsWith`   | `func (s1 string) startsWith(s2 string) bool`            | 判断 s1 是否由 s2 开头                                                   | xray ≥ 1.8.4 |                                             |
| `endsWith`     | `func (s1 string) endsWith(s2 string) bool`              | 判断 s1 是否由 s2 结尾                                                   | xray ≥ 1.8.4 |                                             |
| `basename`     | `func basename(s1 string) string`                        | 返回 URL 的最后一个路径的名称                                                 | xray ≥ 1.9.1 | [🔎](guide/poc/example/string/basename.md)  |
| `dir`          | `func dir(s1 string) string`                             | 返回 URL 的路径                                                        | xray ≥ 1.9.1 | [🔎](guide/poc/example/string/dir.md)       |
| `upper`        | `func upper(s1 string) string`                           | 将 string 中的小写字母转换成大写                                              | xray ≥ 1.9.3 |                                             |
| `rev`          | `func rev(s1 string) string`                             | 将 string 反向输出，主要用于验证命令执行                                          | xray ≥ 1.9.3 |                                             |

## []byte 处理

| 函数名           | 函数原型                                                    | 说明                                                                            | 适用版本         | 详情                                       |
|---------------|---------------------------------------------------------|-------------------------------------------------------------------------------|--------------|------------------------------------------|
| `bcontains`   | `func (b1 bytes) bcontains(b2 bytes) bool`              | 判断一个 b1 是否包含 b2，返回 bool 类型结果。与 contains 不同的是，bcontains 是字节流（bytes）的查找         | xray ≥ 1.8.4 |                                          |
| `ibcontains`  | `func (b1 bytes) ibcontains(b2 bytes) bool`             | 判断 b1 是否包含 b2，返回 bool 类型结果, 与 contains 不同的是，ibcontains 是字节流（bytes）的查找, 且忽略大小写 | xray ≥ 1.8.4 |                                          |
| `bstartsWith` | `func (b1 bytes) bstartsWith(b2 bytes) bool`            | 判断一个 b1 是否由 b2 开头，返回 bool 类型结果。与 startsWith 不同的是，bcontains 是字节流（bytes）的查找     | xray ≥ 1.8.4 |                                          |
| `bformat`     | `func (b1 bytes) bformat(int, int, string, int) string` | 将 bytes 进行进制转换编码，可以根据输入的参数转换成对应的进制编码，并可以自定义转换格式                               | xray ≥ 1.8.5 | [🔎](guide/poc/example/bytes/bformat.md) |

## 编码函数

| 函数名            | 函数原型                                                 | 说明                                                                                 | 适用版本         | 详情                                             |
|----------------|------------------------------------------------------|------------------------------------------------------------------------------------|--------------|------------------------------------------------|
| `base64`       | `func base64(string/bytes) string`                   | 将字符串或 bytes 进行 base64 编码                                                           | xray ≥ 1.8.4 |                                                |
| `base64Decode` | `func base64Decode(string/bytes) string`             | 将字符串或 bytes 进行 base64 解码                                                           | xray ≥ 1.8.4 |                                                |
| `urlencode`    | `func urlencode(string/bytes) string`                | 将字符串或 bytes 进行 urlencode 编码                                                        | xray ≥ 1.8.4 |                                                |
| `urlencodeall` | `func urlencodeall(string/bytes) string`             | 将字符串或 bytes 进行 urlencode 编码, 结果为全字符编码                                              | xray ≥ 1.8.5 |                                                |
| `urldecode`    | `func urldecode(string/bytes) string`                | 将字符串或 bytes 进行 urldecode 解码                                                        | xray ≥ 1.8.4 |                                                |
| `faviconHash`  | `func faviconHash(string/bytes) int`                 | 将字符串或 bytes 进行 faviconHash 编码，参考：[iconhash](https://github.com/Becivells/iconhash) | xray ≥ 1.8.4 |                                                |
| `htmlEscape`   | `func htmlEscape(string/bytes, string, bool) string` | 将字符串或 bytes 进行 htmlEscape 编码，支持 named/numeric/hex 模式，支持全/非全字符编码                    | xray ≥ 1.9.4 | [🔎](guide/poc/example/encode/htmlEscape.md)   |
| `htmlUnescape` | `func htmlUnescape(string/bytes) string`             | 将字符串或 bytes 进行 htmlEscape 解码，支持 named/numeric/hex 模式，支持全/非全字符编码                    | xray ≥ 1.9.4 | [🔎](guide/poc/example/encode/htmlUnescape.md) |
| `hex`          | `func hex(string/bytes) string`                      | 将字符串或 bytes 进行 hex 编码                                                              | xray ≥ 1.9.4 | [🔎](guide/poc/example/encode/hex.md)          |
| `hexDecode`    | `func hexDecode(string/bytes) string`                | 将字符串或 bytes 进行 hex 解码                                                              | xray ≥ 1.9.4 | [🔎](guide/poc/example/encode/hexDecode.md)    |

## 加密函数

| 函数名       | 函数原型                                                     | 说明                                                                                                                                            | 适用版本         |
|-----------|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------|--------------|
| `md5`     | `func md5(string) string`                                | 字符串的 md5                                                                                                                                      | xray ≥ 1.8.4 |
| `sha`     | `func sha(string/bytes, string)string`                   | 该函数可以将指定字符串或 bytes 进行 sha 系列计算，第二个参数控制加密类型。例：sha('asd','sha1')。目前支持'sha1'、'sha224'、'sha256'、'sha384'、'sha512'                                 | xray ≥ 1.8.4 |
| `hmacSha` | `func hmacSha(string/bytes, string/bytes, string)string` | 该函数可以将指定字符串或 bytes 进行 hmac_sha 系列计算，第一个参数为待加密明文，第二个参数为密钥，第三个参数控制加密类型。例：sha('asd','123','sha1')。目前支持'sha1'、'sha224'、'sha256'、'sha384'、'sha512' | xray ≥ 1.8.4 |

## 随机值

| 函数名               | 函数原型                                    | 说明                | 适用版本         |
|-------------------|-----------------------------------------|-------------------|--------------|
| `randomInt`       | `func randomInt(from, to int) int`      | 两个范围内的随机数         | xray ≥ 1.8.4 |
| `randomLowercase` | `func randomLowercase(n length) string` | 指定长度的小写字母组成的随机字符串 | xray ≥ 1.8.4 |

## 正则

| 函数名         | 函数原型                                                       | 说明                                                                                                                   | 适用版本         |
|-------------|------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|--------------|
| `matches`   | `func (s1 string) matches(s2 string) bool`                 | 使用正则表达式 s1 来匹配 s2，返回 bool 类型匹配结果                                                                                     | xray ≥ 1.8.4 |
| `submatch`  | `func (s1 string) submatches(s2 string) map[string]string` | 使用正则表达式 s1 来匹配 s2，返回 map[string]string 类型结果，**注**：只返回具名的正则匹配结果 (?P<name>…) 格式                                        | xray ≥ 1.8.4 |
| `bmatches`  | `func (s1 string) bmatches(b1 bytes) bool`                 | 使用正则表达式 s1 来匹配 b1，返回 bool 类型匹配结果。与 matches 不同的是，bmatches 匹配的是字节流（bytes）                                              | xray ≥ 1.8.4 |
| `bsubmatch` | `func (s1 string) bsubmatches(b1 bytes) map[string]string` | 使用正则表达式 s1 来匹配 b1，返回 map[string]string 类型结果 **注**：只返回具名的正则匹配结果 (?P<name>…) 格式。与 matches 不同的是，bmatches 匹配的是字节流（bytes） | xray ≥ 1.8.4 |

## 反连平台

| 函数名          | 函数原型                                                | 说明                           | 适用版本         |
|--------------|-----------------------------------------------------|------------------------------|--------------|
| `newReverse` | `func newReverse() reverseType`                     | 返回一个 reverse 实例              | xray ≥ 1.8.4 |
| `wait`       | `func (reverse reverseType) wait(timeout int) bool` | 等待 timeout 秒，并返回是否在改时间内获得了信息 | xray ≥ 1.8.4 |

## 时间

| 函数名           | 函数原型                                   | 说明                                      | 适用版本         |                                             |
|---------------|----------------------------------------|-----------------------------------------|--------------|---------------------------------------------|
| `now`         | `func now() Time`                      | 使用该函数将返回当前时间，使用 int(now())，将会获取当前时间的时间戳 | xray ≥ 1.8.5 |                                             |
| `sleep`       | `func sleep(int) bool`                 | 暂停执行等待指定的秒数                             | xray ≥ 1.8.4 |                                             |
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

| 函数名  | 函数原型            | 说明              | 适用版本         |
|------|-----------------|-----------------|--------------|
| `in` | `string in map` | map 中是否包含某个 key | xray ≥ 1.8.4 |
