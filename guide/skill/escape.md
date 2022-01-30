# 头疼的转义

## YAML 本身的转义

一个比较好的答案： [How do I break a string in YAML over multiple lines?](https://stackoverflow.com/questions/3790454/how-do-i-break-a-string-in-yaml-over-multiple-lines/21699210)

我们这里只针对性的讨论一下 `'`, `"`, `\` 和转义字符比如 `\n`, `\r`

推荐的 yaml string 格式用 ``, `"`, `|` 这 3 种形式。

- `|` 或者 `|-` 是不需要考虑 yaml 转义的格式，类似 golang 中的 `
    1. 会保留结尾空格
    1. 会保留换行
    1. 不支持转义
    1. `|` 会保留最后的换行， `|-` 不会
- `"` 是支持转义字符的格式
    1. 换行会变成空格, 如果想不变成空格，需要结尾加 \
    1. 2连换行会变成换行， `\n` 会变成换行
    1. 支持转义, `"` 需要转义
- `` 是不支持转义的，且不支持 yaml 的一些保留字符的比如：`#`, `:` 等
    1. 开头不能是 `'`, `"`
    1. 不得占用 yaml 的关键字符
    1. 不支持转义

**注:** 遇事不决 `hex dump` 一下，各种语言的字符串变量通常还有自己的转义， print 打印出来的字符可能还会被 shell 本身处理，会让事情变得更加复杂

举一些常见的例子：

- 如果不用换行，不用转义, 建议 ``

```yaml
data: a'"\n\
# 00000000  61 27 22 5c 6e 5c                                 |a'"\n\|
```

- 如果希望换行格式好看一些，且也不用转义的话，建议使用 `|` 或者 `|-`

```yaml
data: |
  '
  "  
  \n
  \
# 注意双引号后面跟了 2 个空格
# 00000000  27 0a 22 20 20 0a 5c 6e  0a 5c 0a                 |'."  .\n.\.|
```

```yaml
data: |-
  '
  "  
  \n
  \
# 注意双引号后面跟了 2 个空格
# 和上面比较最后的换行没有了
# 00000000  27 0a 22 20 20 0a 5c 6e  0a 5c                    |'."  .\n.\|
```

- 如果涉及到转义的话，可以使用 `"`

```yaml
data: "a'\"\\n\\\n"
# 00000000  61 27 22 5c 6e 5c 0a                              |a'"\n\.|
```

**注：** 这里解决之前一个遗留的问题：

[multipart/form-data](https://github.com/chaitin/xray/issues/1493#issuecomment-968690547)

可以参考 apache-flink-upload-rce.yml 这个 yaml 文件， 其中需要发送 `multipart/form-data` 的包。它的每一行是要以 `\r\n`（hex `0d` `0a`） 结尾的。

一个错误的写法：(可以关注其中的 `0a`)

```yaml
data: |-
    --8ce4b16b22b58894aa86c421e8759df3
    Content-Disposition: form-data; name="jarfile";filename="{{r2}}.jar"
    Content-Type:application/octet-stream

    {{r1}}
    --8ce4b16b22b58894aa86c421e8759df3--
# 00000000  2d 2d 38 63 65 34 62 31  36 62 32 32 62 35 38 38  |--8ce4b16b22b588|
# 00000010  39 34 61 61 38 36 63 34  32 31 65 38 37 35 39 64  |94aa86c421e8759d|
# 00000020  66 33 0a 43 6f 6e 74 65  6e 74 2d 44 69 73 70 6f  |f3.Content-Dispo|
# 00000030  73 69 74 69 6f 6e 3a 20  66 6f 72 6d 2d 64 61 74  |sition: form-dat|
# 00000040  61 3b 20 6e 61 6d 65 3d  22 6a 61 72 66 69 6c 65  |a; name="jarfile|
# 00000050  22 3b 66 69 6c 65 6e 61  6d 65 3d 22 7b 7b 72 32  |";filename="{{r2|
# 00000060  7d 7d 2e 6a 61 72 22 0a  43 6f 6e 74 65 6e 74 2d  |}}.jar".Content-|
# 00000070  54 79 70 65 3a 61 70 70  6c 69 63 61 74 69 6f 6e  |Type:application|
# 00000080  2f 6f 63 74 65 74 2d 73  74 72 65 61 6d 0a 0a 7b  |/octet-stream..{|
# 00000090  7b 72 31 7d 7d 0a 2d 2d  38 63 65 34 62 31 36 62  |{r1}}.--8ce4b16b|
# 000000a0  32 32 62 35 38 38 39 34  61 61 38 36 63 34 32 31  |22b58894aa86c421|
# 000000b0  65 38 37 35 39 64 66 33  2d 2d                    |e8759df3--|
```

我们可以用 `"` 来表示此包。需要做的事情是：

1. 转义掉其中的 `"`
1. 正确的处理换行：
    1. 换行会变成空格, 如果想不变成空格，需要结尾加 \
    1. 2连换行会变成换行， `\n` 会变成换行

推荐的写法：

```yaml
data: "\
    --8ce4b16b22b58894aa86c421e8759df3\r\n\ 
    Content-Disposition: form-data; name=\"jarfile\";filename=\"{{r2}}.jar\"\r\n\
    Content-Type:application/octet-stream\r\n\
    \r\n\
    {{r1}}\r\n\
    --8ce4b16b22b58894aa86c421e8759df3--\r\n\
    "
# 00000000  2d 2d 38 63 65 34 62 31  36 62 32 32 62 35 38 38  |--8ce4b16b22b588|
# 00000010  39 34 61 61 38 36 63 34  32 31 65 38 37 35 39 64  |94aa86c421e8759d|
# 00000020  66 33 0d 0a 43 6f 6e 74  65 6e 74 2d 44 69 73 70  |f3..Content-Disp|
# 00000030  6f 73 69 74 69 6f 6e 3a  20 66 6f 72 6d 2d 64 61  |osition: form-da|
# 00000040  74 61 3b 20 6e 61 6d 65  3d 22 6a 61 72 66 69 6c  |ta; name="jarfil|
# 00000050  65 22 3b 66 69 6c 65 6e  61 6d 65 3d 22 7b 7b 72  |e";filename="{{r|
# 00000060  32 7d 7d 2e 6a 61 72 22  0d 0a 43 6f 6e 74 65 6e  |2}}.jar"..Conten|
# 00000070  74 2d 54 79 70 65 3a 61  70 70 6c 69 63 61 74 69  |t-Type:applicati|
# 00000080  6f 6e 2f 6f 63 74 65 74  2d 73 74 72 65 61 6d 0d  |on/octet-stream.|
# 00000090  0a 0d 0a 7b 7b 72 31 7d  7d 0d 0a 2d 2d 38 63 65  |...{{r1}}..--8ce|
# 000000a0  34 62 31 36 62 32 32 62  35 38 38 39 34 61 61 38  |4b16b22b58894aa8|
# 000000b0  36 63 34 32 31 65 38 37  35 39 64 66 33 2d 2d 0d  |6c421e8759df3--.|
# 000000b0  0a                                                |.|
```

PS：也可以写在一行，就是比较难读

PSS： 要是愿意的话，也可以用 2 连换行来表示 \n 不过也有点难看就是了 -_-

## CEL 本身的转义

[cel 转义](https://github.com/google/cel-spec/blob/master/doc/langdef.md#string-and-bytes-values)

这里就不赘述 cel 本身的转义了，主要讨论几个在 yaml 中写 cel 的情况。最简单的方式就是 yaml 采用不需要转义的方式 `` 或者 `|`

- cel 也不需要转义

```yaml
data: r"'\n"
# r" 支持输入不含 “ 的非转义字符串
# 00000000  27 5c 6e                                          |'\n|
```

```yaml
data: r'"\n'
# r' 支持输入不含 ' 的非转义字符串
# 00000000  22 5c 6e                                          |"\n|
```

- yaml 不转义, CEL 转义

```yaml
data: |
  "'\"\r\n"
# r' 支持输入不含 ' 的非转义字符串
# 00000000  27 22 0d 0a                                       |'"..|
```

## 正则的转义

CEL 支持输入正则，来使用 `match` 方法等，不幸的是正则也需要转义。推荐:

1. yaml 采用不需要转义的方式 `` 或者 `|`
2. cel 也采用不需要转义的方式 `r'` 或者 `r"`

一些例子：

- 正则不转义

```yaml
data: r'aa'.matches(a)
# golang:
# a := "aa" 匹配成功
```

- 正则转义

```yaml
data: r'\r\n'.match(a)
# golang
# a := "\r\n" 匹配成功
```

```yaml
data: |
    r'\\r'.match(a)
# golang:
# a := `\r` 匹配成功
```

一个复杂的例子：

```yaml
data: |
    "product:\\['c=\"a\"'\\]".matches(a)
# golang:
# a:= `product:['c="a"']` 匹配成功
```

思路讲解：

1. 考虑正则应该如何写, 该例子需要转义 `[`： `product:\['c="a"'\]`
2. 考虑 cel 转义, 因为含有 `'` `"`, 所以必须要转义， 选择 `"` 的方式，需要转义 `"` 和 `\`
    1. 首先转义 `\`: `product:\\['c="a"'\\]`
    1. 然后转义 `"`: `product:\\['c=\"a\"'\\]`
    1. 最后用双引包裹: `"product:\\['c=\"a\"'\\]".matches(a)`
3. 考虑 yaml 转义，选择 `|` 不需要转义， 最终：`"product:\\['c=\"a\"'\\]".matches(a)`

PS(大脑升级):

就要 yaml 用 `"` 格式，那么会是什么样子的呢？

1. 考虑正则应该如何写, 该例子需要转义 `[`： `product:\['c="a"'\]`
1. 考虑 cel 转义, 因为含有 `'` `"`, 所以必须要转义， 选择 `"` 的方式，需要转义 `"` 和 `\`
    1. 首先转义 `\`: `product:\\['c="a"'\\]`
    1. 然后转义 `"`: `product:\\['c=\"a\"'\\]`
    1. 最后用双引包裹: `"product:\\['c=\"a\"'\\]".matches(a)`
1. 考虑 yaml 转义，需要转义 `\`, `"`
    1. 首先转义 `\`: `"product:\\\\['c=\\"a\\"'\\\\]".matches(a)`
    1. 其次转义 `"`: `\"product:\\\\['c=\\\"a\\\"'\\\\]\".matches(a)`
    1. 最后用 `"` 包裹： `"\"product:\\\\['c=\\\"a\\\"'\\\\]\".matches(a)"`

```yaml
data: "\"product:\\\\['c=\\\"a\\\"'\\\\]\".matches(a)"
# golang:
# a:= `product:['c="a"']` 匹配成功
```
