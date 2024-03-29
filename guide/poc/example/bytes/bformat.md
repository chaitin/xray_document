# 函数介绍

`bformat`主要是用来对`bytes`类型的数据进行进制转换，并可以自定义输出样式

## 函数原型

`func (b1 bytes) bformat(int, int, string, int) string`

### 参数介绍

| 参数名称 | 参数介绍                          |
|------|-------------------------------|
| b1   | 待转化的数据                        |
| 进制   | 要转换的进制                        |
| 填充位数 | 如果转换后的字符的位数小于该值，则会在前面添加0来进行填充 |
| 填充字符 | 在转换后的字符前添加指定字符串，例如`\x`等       |
| 填充位置 | 控制第三，四个参数添加的位置                |
| 输出   | 输出结果为字符串                      |

## 举例

| 待转换数据                      | 转换语句                                                  | 输出结果                           |
|----------------------------|-------------------------------------------------------|--------------------------------|
| `asdfghj`                  | `b'asdfghj'.bformat(16,0,'',0)`                       | `6173646667686a`               |
| `asdfghj`                  | `b'asdfghj'.bformat(16,4,'',0)`                       | `006100730064006600670068006a` |
| `asdfghj`                  | `b'asdfghj'.bformat(16,0,'\\\x',1)`                   | `\x61\x73\x64\x66\x67\x68\x6a` |
| `asdfghj`                  | `b'asdfghj'.bformat(8, 0, "0", 1)`                    | `0141016301440146014701500152` |
| `\x00\x01\x02\x03\x04\x0a` | `b'\x00\x01\x02\x03\x04\x0a'.bformat(2, 2, "\\b", 1)` | `\b00\b01\b10\b11\b100\b1010`  |