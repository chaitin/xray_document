# 函数介绍

`basename`函数主要是用来返回 URL 的最后一个路径的名称

## 函数原型

`func basename(s1 string) string`

### 参数介绍

| 参数名称 | 参数介绍   |
|------|--------|
| s1   | 待处理的路径 |

## 举例


| 待转换数据                            | 转换语句                                         | 输出结果    |
|----------------------------------|----------------------------------------------|---------|
| `/a/b`                           | `basename("/a/b")`                           | `b`     |
| `https://docs.xray.cool/a/b.zip` | `basename("https://docs.xray.cool/a/b.zip")` | `b.zip` |
| `/a/`                            | `basename("/a/")`                            | `""`    |
| `/`                              | `basename("/")`                              | `""`    |
| `""`                             | `basename("")`                               | `""`    |

