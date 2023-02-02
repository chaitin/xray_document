# xray 脚本注入的函数

xray 支持所有CEL文档中的函数，同时还新增了一些函数支持，下面列举一下常用的函数：

**注**：gocel 原生的正则支持使用的 golang 自带的 regex 库， 在脚本中我们使用 [regex2](github.com/dlclark/regexp2) 替换了自带的 regex 库

## rule函数

**注**：只有脚本层级的 expression 才能使用这些函数

| 函数名         | 函数原型                      | 说明                         | 适用版本         |
|-------------|---------------------------|----------------------------|--------------|
| `rule name` | `func <rule name>() bool` | 返回这条 rule expression 执行的结果 | xray ≥ 1.8.4 |

## 字符串处理

| 函数名            | 函数原型                                                     | 说明                                                                | 适用版本         |
|----------------|----------------------------------------------------------|-------------------------------------------------------------------|--------------|
| `contains`     | `func (s1 string) contains(s2 string) bool`              | 判断s1是否包含s2，返回bool类型结果                                             | xray ≥ 1.8.4 |
| `icontains`    | `func (s1 string) icontains(s2 string) bool`             | 判断s1是否包含s2，返回bool类型结果, 与contains不同的是，icontains 忽略大小写              | xray ≥ 1.8.4 |
| `substr`       | `func substr(string, start int, length int) string`      | 截取字符串                                                             | xray ≥ 1.8.4 |
| `replaceAll`   | `func replaceAll(string, old string, new string) string` | 将 string 中的 old 替换为 new，返回替换后的 string                             | xray ≥ 1.8.4 |
| `printable`    | `func printable(string) string`                          | 将 string 中的非 unicode 编码字符去掉                                       | xray ≥ 1.8.4 |
| `toUintString` | `func toUintString(s1 string, direction string) string`  | direction 取值为 `>`,`<`表示读取方向, 将 s1 按 direction 读取为一个整数，返回该整数的字符串形式 | xray ≥ 1.8.4 |
| `startsWith`   | `func (s1 string) startsWith(s2 string) bool`            | 判断s1是否由s2开头                                                       | xray ≥ 1.8.4 |
| `endsWith`     | `func (s1 string) endsWith(s2 string) bool`              | 判断s1是否由s2结尾                                                       | xray ≥ 1.8.4 |
| `basename`     | `func basename(s1 string) string`                        | 返回URL的最后一个路径的名称                                                   | xray ≥ 1.9.1 |
| `dir`          | `func dir(s1 string) string`                             | 返回URL的路径                                                          | xray ≥ 1.9.1 |
| `upper`        | `func upper(s1 string) string`                           | 将 string 中的小写字母转换成大写                                              | xray ≥ 1.9.3 |
| `rev`          | `func rev(s1 string) string`                             | 将 string 反向输出，主要用于验证命令执行                                          | xray ≥ 1.9.3 |

basename:
```
basename("/a/b") => "b"
basename("/a/") => ""
basename("/") => ""
basename("") => ""
```
dir:
```
dir("/a/b") => "/a/"
dir("a/b/c") => "a/b/"
dir("a/") => "a/"
dir("/") => "/"
dir("/a") => "/"
dir("a") => ""
dir("") => ""
dir("./../../a") => "./../../"
```

## []byte 处理

| 函数名           | 函数原型                                                    | 说明                                                                     | 适用版本         | 详情                                       |
|---------------|---------------------------------------------------------|------------------------------------------------------------------------|--------------|------------------------------------------|
| `bcontains`   | `func (b1 bytes) bcontains(b2 bytes) bool`              | 判断一个b1是否包含b2，返回bool类型结果。与contains不同的是，bcontains是字节流（bytes）的查找          | xray ≥ 1.8.4 | [🔎]()                                   |
| `ibcontains`  | `func (b1 bytes) ibcontains(b2 bytes) bool`             | 判断b1是否包含b2，返回bool类型结果, 与contains不同的是，ibcontains 是字节流（bytes）的查找, 且忽略大小写 | xray ≥ 1.8.4 | [🔎]()                                   |
| `bstartsWith` | `func (b1 bytes) bstartsWith(b2 bytes) bool`            | 判断一个b1是否由b2开头，返回bool类型结果。与startsWith不同的是，bcontains是字节流（bytes）的查找       | xray ≥ 1.8.4 | [🔎]()                                   |
| `bformat`     | `func (b1 bytes) bformat(int, int, string, int) string` | 将bytes进行进制转换编码，可以根据输入的参数转换成对应的进制编码，并可以自定义转换格式                          | xray ≥ 1.8.5 | [🔎](guide/poc/example/bytes/bformat.md) |

## 编码加密函数

| 函数名            | 函数原型                                                     | 说明                                                                                                                                        | 适用版本         |
|----------------|----------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|--------------|
| `md5`          | `func md5(string) string`                                | 字符串的 md5                                                                                                                                  | xray ≥ 1.8.4 |
| `base64`       | `func base64(string/bytes) string`                       | 将字符串或 bytes 进行 base64 编码                                                                                                                  | xray ≥ 1.8.4 |
| `base64Decode` | `func base64Decode(string/bytes) string`                 | 将字符串或 bytes 进行 base64 解码                                                                                                                  | xray ≥ 1.8.4 |
| `urlencode`    | `func urlencode(string/bytes) string`                    | 将字符串或 bytes 进行 urlencode 编码                                                                                                               | xray ≥ 1.8.4 |
| `urlencodeall` | `func urlencodeall(string/bytes) string`                 | 将字符串或 bytes 进行 urlencode 编码, 结果为全字符编码                                                                                                     | xray ≥ 1.8.5 |
| `urldecode`    | `func urldecode(string/bytes) string`                    | 将字符串或 bytes 进行 urldecode 解码                                                                                                               | xray ≥ 1.8.4 |
| `faviconHash`  | `func faviconHash(string/bytes) int`                     | 将字符串或 bytes 进行 faviconHash 编码，参考：[iconhash](https://github.com/Becivells/iconhash)                                                        | xray ≥ 1.8.4 |
| `sha`          | `func sha(string/bytes, string)string`                   | 该函数可以将指定字符串或bytes进行sha系列计算，第二个参数控制加密类型。例：sha('asd','sha1')。目前支持'sha1'、'sha224'、'sha256'、'sha384'、'sha512'                                 | xray ≥ 1.8.4 |
| `hmacSha`      | `func hmacSha(string/bytes, string/bytes, string)string` | 该函数可以将指定字符串或bytes进行hmac_sha系列计算，第一个参数为待加密明文，第二个参数为密钥，第三个参数控制加密类型。例：sha('asd','123','sha1')。目前支持'sha1'、'sha224'、'sha256'、'sha384'、'sha512' | xray ≥ 1.8.4 |

## 随机值

| 函数名               | 函数原型                                    | 说明                | 适用版本         |
|-------------------|-----------------------------------------|-------------------|--------------|
| `randomInt`       | `func randomInt(from, to int) int`      | 两个范围内的随机数         | xray ≥ 1.8.4 |
| `randomLowercase` | `func randomLowercase(n length) string` | 指定长度的小写字母组成的随机字符串 | xray ≥ 1.8.4 |

## 正则

| 函数名         | 函数原型                                                       | 说明                                                                                                             | 适用版本         |
|-------------|------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------|--------------|
| `matches`   | `func (s1 string) matches(s2 string) bool`                 | 使用正则表达式s1来匹配s2，返回bool类型匹配结果                                                                                    | xray ≥ 1.8.4 |
| `submatch`  | `func (s1 string) submatches(s2 string) map[string]string` | 使用正则表达式s1来匹配s2，返回 map[string]string 类型结果，**注**：只返回具名的正则匹配结果 (?P<name>…) 格式                                     | xray ≥ 1.8.4 |
| `bmatches`  | `func (s1 string) bmatches(b1 bytes) bool`                 | 使用正则表达式s1来匹配b1，返回bool类型匹配结果。与matches不同的是，bmatches匹配的是字节流（bytes）                                                | xray ≥ 1.8.4 |
| `bsubmatch` | `func (s1 string) bsubmatches(b1 bytes) map[string]string` | 使用正则表达式s1来匹配b1，返回 map[string]string 类型结果 **注**：只返回具名的正则匹配结果 (?P<name>…) 格式。与matches不同的是，bmatches匹配的是字节流（bytes） | xray ≥ 1.8.4 |

## 反连平台

| 函数名          | 函数原型                                                | 说明                           | 适用版本         |
|--------------|-----------------------------------------------------|------------------------------|--------------|
| `newReverse` | `func newReverse() reverseType`                     | 返回一个 reverse 实例              | xray ≥ 1.8.4 |
| `wait`       | `func (reverse reverseType) wait(timeout int) bool` | 等待 timeout 秒，并返回是否在改时间内获得了信息 | xray ≥ 1.8.4 |

## 时间

| 函数名           | 函数原型                                   | 说明                                     | 适用版本         |
|---------------|----------------------------------------|----------------------------------------|--------------|
| `now`         | `func now() Time`                      | 使用该函数将返回当前时间，使用int(now())，将会获取当前时间的时间戳 | xray ≥ 1.8.5 |
| `sleep`       | `func sleep(int) bool`                 | 暂停执行等待指定的秒数                            | xray ≥ 1.8.4 |
| `timeConvert` | `func timeConvert(int, string) string` | 该函数可以将指定时间戳转换成自定义格式的字符串                | xray ≥ 1.8.5 |

* timeConvert本质上是使用golang的time包提供的Format方法，第一个参数传入时间戳，第二个参数传入想要函数输出的时间格式
* 以下是golang中time包对于时间的详细定义
  代表 | 写法
  ---- | ----
  ⽉份 | 1,01,Jan,January
  ⽇ | 2,02,_2
  时 | 3,03,15,PM,pm,AM,am
  分 | 4,04
  秒 | 5,05
  年 | 06,2006
  时区 | -07,-0700,Z0700,Z07:00,-07:00,MST
  周⼏ | Mon,Monday

    * **⽐如⼩时的表⽰**(原定义是下午3时，也就是15时)
      3 ⽤12⼩时制表⽰，去掉前导0
      03 ⽤12⼩时制表⽰，保留前导0
      15 ⽤24⼩时制表⽰，保留前导0
      03pm ⽤24⼩时制am/pm表⽰上下午表⽰，保留前导0
      3pm ⽤24⼩时制am/pm表⽰上下午表⽰，去掉前导0

    * **⼜⽐如⽉份**
      1 数字表⽰⽉份，去掉前导0
      01 数字表⽰⽉份，保留前导0
      Jan 缩写单词表⽰⽉份
      January 全单词表⽰⽉份
* **示例**：timeConvert(1560391089, '2006_01_02/3/4/05')  
  **输出**：2019_06_13/9/58/09

## 其它

| 函数名  | 函数原型            | 说明              | 适用版本         |
|------|-----------------|-----------------|--------------|
| `in` | `string in map` | map 中是否包含某个 key | xray ≥ 1.8.4 |
