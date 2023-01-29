# 函数介绍

`printable`函数主要是用来将 string 中的非 unicode 编码字符去掉

## 函数原型

`func printable(s1 string) s2 string`

### 参数介绍

| 参数名称 | 参数介绍  |
|------|-------|
| s1   | 待转换数据 |
| s2   | 输出    |

## 举例


| 待转换数据            | 转换语句                          | 输出结果 |
|------------------|-------------------------------|------|
| `B\x0a\x0b\x0cA` | `printable("B\x0a\x0b\x0cA")` | `BA` |
