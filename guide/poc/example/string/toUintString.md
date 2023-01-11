# 函数介绍

`toUintString`函数主要是用来将传入的字符串按 direction 读取为一个整数，返回该整数的字符串形式

## 函数原型

`func toUintString(s1 string, direction string) string`

### 参数介绍

| 参数名称      | 参数介绍        |
|-----------|-------------|
| s1        | 待转换数据       |
| direction | 按照什么字节序进行转换 |

## 举例


| 待转换数据  | 转换语句                        | 输出结果         |
|--------|-----------------------------|--------------|
| `BABC` | `toUintString("BABC", "<")` | `1128415554` |
| `BABC` | `toUintString("BABC", ">")` | `1111573059` |


