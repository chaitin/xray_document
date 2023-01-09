# 函数介绍

`hexDecode`函数主要是用来将所给字符串或者字节数组进行十六进制解码，并输出解码后的字符串

## 函数原型

- `func hexDecode(s1 string) string`
- `func hexDecode(b1 bytes) string`

### 参数介绍

| 参数名称 | 参数介绍                           |
| -------- | ---------------------------------- |
| `s1/b1`  | 为需要进行编码的字符串或者字节数组 |

## 举例

| 待转换数据            | 转换语句                         | 输出结果   |
| --------------------- | -------------------------------- | ---------- |
| `"61736466"`          | `hexDecode("61736466")`          | `asdf`     |
| `b"733B6529392a2642"` | `hexDecode(b"733B6529392a2642")` | `s;e)9*&B` |