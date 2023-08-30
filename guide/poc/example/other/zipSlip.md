# 函数介绍

`zipSlip`函数主要是用来自定义生成zip文件

## 函数原型

`func zipSlip(s1 string, b1 bytes) bytes`

### 参数介绍

| 参数名称 | 参数介绍   |
|------|--------|
| s1   | 文件路径   |
| b1   | 文件内容数据 |

## 举例

`zipSlip("../../../../../../../../../../../../../opt/tomcat/webapps/upload/ssctyjxq.jsp", b"<% out.print(\"this_is_for_test\")%>")`

