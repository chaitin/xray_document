# 函数介绍

`get404Path`函数主要是用来获取一个长度为8的随机字符串，当作404页面的路径。之所以做成一个函数，是因为使用这个函数可以保证一个目标将仅拥有一个404path
，这样便于使用cache，降低发包，提高效率。

## 函数原型

`func get404Path() string`

## 举例

```yaml
name: poc-yaml-test
manual: true
transport: http
set:
  randomPath: get404Path()
rules:
  r0:
    request:
      cache: true
      method: GET
      path: /{{randomPath}}
    expression: response.status == 404
expression: r0()
detail:
  author: test
  links:
    - https://www.test.com
```
