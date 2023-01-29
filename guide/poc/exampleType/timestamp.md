# 介绍

`Timestamp`类型实际是google.protobuf.Timestamp类型，为cel表达式本身自带的类型

## 类型介绍

介绍举例时间点将采用时间戳为`1234567890`,时间为`2009-02-13T23:31:30Z`进行举例

| 方法              | 方法介绍                  | 使用举例                                              | 返回值          | 完整示例                                                                                                                  |
|-----------------|-----------------------|---------------------------------------------------|--------------|-----------------------------------------------------------------------------------------------------------------------|
| `int()`         | 将Timestamp类型转换为整型的时间戳 | `int(timestamp('2009-02-13T23:31:30Z'))`          | `1234567890` | [timestamp_conversions](https://github.com/google/cel-spec/blob/master/tests/simple/testdata/timestamps.textproto#L3) |
| `getFullYear()` | 获取这个时间的年份             | `timestamp('2009-02-13T23:31:30Z').getFullYear()` | `2009`       | [timestamp_selectors](https://github.com/google/cel-spec/blob/master/tests/simple/testdata/timestamps.textproto#L42)  |

## 其他功能

- [时间对比](https://github.com/google/cel-spec/blob/master/tests/simple/testdata/timestamps.textproto#L244)
- [时间运算](https://github.com/google/cel-spec/blob/master/tests/simple/testdata/timestamps.textproto#L210)

完整功能请参考文档[timestamps.textproto](https://github.com/google/cel-spec/blob/master/tests/simple/testdata/timestamps.textproto)