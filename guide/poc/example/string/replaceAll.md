# 函数介绍

`replaceAll`函数主要是用来将 string 中的 old 替换为 new，返回替换后的 string

## 函数原型

`func replaceAll(s1 string, old string, new string) string`

### 参数介绍

| 参数名称 | 参数介绍      |
|------|-----------|
| s1   | 待替换的数据    |
| old  | 需要被替换掉的数据 |
| new  | 替换的新数据    |

## 举例


| 待转换数据                    | 转换语句                                                 | 输出结果                     |
|--------------------------|------------------------------------------------------|--------------------------|
| `asdfgfhjkq asdef vjrta` | `replaceAll("asdfgfhjkq asdef vjrta", "asd", "AAA")` | `AAAfgfhjkq AAAef vjrta` |
| `10.3.5-5.2`             | `replaceAll("10.3.5-5.2", ".", "-")`                 | `10-3-5-5-2`             |


