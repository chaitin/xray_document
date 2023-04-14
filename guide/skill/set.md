# 定义变量

?> 此处的规则同样适用于expression、output中的变量定义和使用

## set中引用变量

在定义变量的时候，可以使用其他变量的值，如下所示：

### 变量间的引用

```yaml
set:
  s1: randomLowercase(10)
  base64s1: base64(s1)
  s2: base64s1 + "AAA"
```

在上述的例子中，我们定义了三个变量，其中 `s2` 的值是 `base64s1` 的值加上 `AAA`。由此可知，在set中，引用一个变量的值，不需要使用`{{}}`
将变量包裹起来，直接使用变量名称即可。从这个规则我们也可以引申出，在expression、output中，引用变量的值，也不需要使用`{{}}`将变量包裹起来，直接使用变量名称即可。

### 定义字符串的方式

在定义字符串的时候，有三种方式，如下所示：
```yaml
name: poc-yaml-test
transport: http
set:
  s1: string("hello world")
  s2: |
    "hello world"
  s3: '"hello world"'
rules:
  r0:
    request:
      method: POST
      path: /
      follow_redirects: false
      headers:
        Content-Type: text/xml
      body: |
        {{s1}} + {{s2}} + {{s3}}
    expression: response.status == 200
expression: r0()
detail:
  author: test
```
发出的请求如下：
```HTTP
POST / HTTP/1.1
Host: docs.xray.cool
Content-Length: 40
Content-Type: text/xml

hello world + hello world + hello world
```
可以发现三种形式都能够正常输出字符串，但是如果只使用单引号或者双引号作为开头，将会引起加载错误，比如：
```yaml
set:
  s1: "hello world"
  s2: 'hello world'
```
使用这样的写法就将加载失败。

## 三目运算符的使用方式

### 介绍

在许多编程语言中，三目运算符（也称为条件运算符）是一种简洁的表达条件逻辑的方式。在 CEL（Common Expression Language）中，三目运算符也是一种常用的表达式，其语法如下：
```yaml
条件表达式 ? 表达式1 : 表达式2
```
三目运算符的工作原理是，当条件表达式的结果为 true 时，整个表达式的值等于表达式1的值；当条件表达式的结果为 false 时，整个表达式的值等于表达式2的值。

例如，在 CEL 表达式中，你可以这样使用三目运算符：
```yaml
age >= 18 ? "Adult" : "Minor"
```
在这个例子中，如果 age 大于或等于 18，表达式的值将为 "Adult"，否则将为 "Minor"。这与以下的 if-else 语句相似：
```go
if age >= 18 {
    return "Adult"
} else {
    return "Minor"
}
```

### 案例

[//]: # (TODO)
