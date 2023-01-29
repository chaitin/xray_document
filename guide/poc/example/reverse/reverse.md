# 函数介绍

`wait`函数主要是用来告诉扫描器要等待多长时间，并返回是否在该时间内获得了信息

## 函数原型

`func (reverse reverseType) wait(timeout int) bool`

### 参数介绍

| 参数名称    | 参数介绍                                    |
|---------|-----------------------------------------|
| reverse | 该函数是reverseType结构体的方法，所以只能由该结构体的变量调用该方法 |
| timeout | 告诉扫描器要等待多长时间                            |

## [举例](guide/poc/exampleType/reverse?id=常见使用场景)
