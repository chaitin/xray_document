# 函数介绍

`substr`函数主要是用来对目标字符串进行截取

## 函数原型

`func substr(s1 string, start int, length int) string`

### 参数介绍

| 参数名称   | 参数介绍      |
|--------|-----------|
| s1     | 需要被分割的字符串 |
| start  | 开始分割的位置   |
| length | 分割长度      |

## 举例

| 待转换数据     | 转换语句                         | 输出结果                            |
|-----------|------------------------------|---------------------------------|
| `abcdefg` | `substr("abcdefg", 1, 2)`    | `bc`                            |
| `abcdefg` | `substr("abcdefg", 100, 2)`  | `""`                            |
| `abcdefg` | `substr("abcdefg", 1, 6)`    | `bcdefg`                        |
| `abcdefg` | `substr("abcdefg", 1, 8)`    | `bcdefg`                        |
| `abcdefg` | `substr("abcdefg", -1, 100)` | `Error: "invalid substr index"` |
| `abcdefg` | `substr("abcdefg", 1, -100)` | `Error: "invalid substr index"` |
| `abcdefg` | `substr("abcdefg", 0, 0)`    | `Error: "invalid substr index"` |


