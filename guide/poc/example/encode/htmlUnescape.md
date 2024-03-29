# 函数介绍

`htmlUnescape`函数主要是用来将所给字符串或者字节数组进行 html 实体解码，并输出解码后的字符串

## 函数原型

- `func htmlUnescape(s1 string) string`
- `func htmlUnescape(b1 bytes) string`

### 参数介绍

| 参数名称 | 参数介绍                           |
| -------- | ---------------------------------- |
| `s1/b1`  | 为需要进行解码的字符串或者字节数组 |

## 举例

| 待转换数据                                                              | 转换语句                                                                              | 输出结果                                        |
| ----------------------------------------------------------------------- | ------------------------------------------------------------------------------------- | ----------------------------------------------- |
| `"&#x3c;&#x6d;&#x61;&#x70;&#x3e;"`                                      | `htmlUnescape("&#x3c;&#x6d;&#x61;&#x70;&#x3e;")`                                      | `<map>`                                         |
| `b"&#34;Fran &amp; Freddie&#39;s Diner&#34; &lt;tasty@example.com&gt;"` | `htmlUnescape(b"&#34;Fran &amp; Freddie&#39;s Diner&#34; &lt;tasty@example.com&gt;")` | `"Fran & Freddie's Diner" <tasty@example.com>"` |
| `"&#60;&#109;&#97;&#112;&#62;"`                                         | `htmlUnescape("&#60;&#109;&#97;&#112;&#62;")`                                         | `<map>`                                         |
