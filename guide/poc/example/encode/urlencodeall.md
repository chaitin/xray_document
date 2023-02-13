# 函数介绍

`urlencodeall`函数主要是用来将字符串或 bytes 进行 urlencode 编码, 结果为全字符编码

## 函数原型

`func urlencodeall(v1 string/bytes) string`

### 参数介绍

| 参数名称 | 参数介绍  |
|------|-------|
| v1   | 待转换数据 |

## 举例

| 待转换数据                              | 转换语句                                            | 输出结果                                     |
|------------------------------------|-------------------------------------------------|------------------------------------------|
| `http://example.com?a=b`           | `urlencode("http://example.com?a=b")`           | `http%3A%2F%2Fexample.com%3Fa%3Db`       |
| `YGVjaG8gemZpaSA+IHZyZHoudHh0YA==` | `urlencode("YGVjaG8gemZpaSA+IHZyZHoudHh0YA==")` | `YGVjaG8gemZpaSA%2BIHZyZHoudHh0YA%3D%3D` |
| `b"http://example.com?a=b"`        | `urlencode(b"http://example.com?a=b")`          | `http%3A%2F%2Fexample.com%3Fa%3Db`       |
