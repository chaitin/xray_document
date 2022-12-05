# 如何编写一个高质量&可被收录的PoC

### 相关视频教程：
[如何编写一个高质量&可被收录的PoC](https://www.bilibili.com/video/BV1KZ4y1Y7Sj)

### 1. 写一个PoC所能得到的收获

1. 在编写POC的过程中，势必要理解这个POC的检测原理，并搭建对应的漏洞环境，每编写一次POC，都是一次学习，对漏洞的理解也会更深入，也会见识到更多的漏洞，提升自己的安全能力。
2. 一个好的POC在被收录后，会合并到Xray后续的更新版本中，会有其他的Xray使用者使用到编写者编写的POC，提升编写者在圈内的名气
3. 将编写好的POC提交到CT stack社区，将会得到10-500金币不等的基础金币奖励。这些金币可以在CT stack社区兑换Xray高级版，Chaitin周边，京东卡等礼品。

### 2. PoC与Exp的概念

**POC(Proof of Concept) - 利用证明**

- `POC`，`Proof of Concept`，意思是 `利用证明`。这个短语会在漏洞报告中使用，漏洞报告中的POC则是一段说明或者一个攻击的漏洞介绍，使得读者能够确认这个`漏洞是真实存在的`。

**EXP(Exploit) - 漏洞利用**

- `EXP`，`Exploit` 中文意思是 `漏洞利用`。意思是一段对漏洞`如何利用的详细说明或者一个演示的漏洞攻击代码`，可以使得读者`完全了解漏洞`的机理以及`利用的方法`。

例如：某网站存在一个命令执行漏洞，那么写脚本时，执行的命令为计算两个随机数的和，这个脚本就是poc脚本；而执行的命令为反弹shell，那这个脚本就是exp脚本。

### 3. yaml文件的科普

目前Xray的POC主要是使用YAML进行编写，所以我们在学习编写POC前，先来学习一下YAML

YAML 的语法和其他高级语言类似，并且可以简单表达清单、散列表，标量等数据形态。它使用空白符号缩进和大量依赖外观的特色，特别适合用来表达或编辑数据结构、各种配置文件、倾印调试内容、文件大纲（例如：许多电子邮件标题格式和YAML非常接近）。

#### 基本语法

- 大小写敏感
- 使用缩进表示层级关系
- 缩进不允许使用tab，只允许空格
- 缩进的空格数不重要，只要相同层级的元素左对齐即可
- '#'表示注释

#### YAML的基本数据类型

- 纯量：单个的、不可再分的值
- 数组：一组按次序排列的值，又称为序列（sequence） / 列表（list）
- 对象：键值对的集合，又称为映射（mapping）/ 哈希（hashes） / 字典（dictionary）

#### 纯量

纯量是最基本的，不可再分的值，包括：

- 字符串
- 布尔值
- 整数
- 浮点数
- Null
- 时间
- 日期

使用一个例子来快速了解纯量的基本使用：

```yaml
boolean: 
    - TRUE  #true,True都可以
    - FALSE  #false，False都可以
float:
    - 3.14
    - 6.8523015e+5  #可以使用科学计数法
int:
    - 123
    - 0b1010_0111_0100_1010_1110    #二进制表示
null:
    nodeName: 'node'
    parent: ~  #使用~表示null
string:
    - 哈哈
    - 'Hello world'  #可以使用双引号或者单引号包裹特殊字符
    - newline
      newline2    #字符串可以拆成多行，每一行会被转化成一个空格
date:
    - 2018-02-17    #日期必须使用ISO 8601格式，即yyyy-MM-dd
datetime: 
    -  2018-02-17T15:02:31+08:00    #时间使用ISO 8601格式，时间和日期之间使用T连接，最后使用+代表时区
```

#### YAML 数组

以 - 开头的行表示构成一个数组：

```yaml
- A
- B
- C
```

YAML 支持多维数组，可以使用行内表示：

```yaml
key: [value1, value2, ...]
```

数据结构的子成员是一个数组，则可以在该项下面缩进一个空格。

```yaml
-
 - A
 - B
 - C
```

一个相对复杂的例子：

```yaml
companies:
    -
        id: 1
        name: company1
        price: 200W
    -
        id: 2
        name: company2
        price: 500W
```

意思是 companies 属性是一个数组，每一个数组元素又是由 id、name、price 三个属性构成。

数组也可以使用流式(flow)的方式表示：

```json
companies: [{id: 1,name: company1,price: 200W},{id: 2,name: company2,price: 500W}]
```

#### YAML 对象

对象键值对使用冒号结构表示 key: value，冒号后面要加一个空格。

也可以使用 key:{key1: value1, key2: value2, ...}。

还可以使用缩进表示层级关系；

```
key: 
    child-key: value
    child-key2: value2
```

较为复杂的对象格式，可以使用问号加一个空格代表一个复杂的 key，配合一个冒号加一个空格代表一个 value：

```
?
    - complexkey1
    - complexkey2
:
    - complexvalue1
    - complexvalue2
```

意思即对象的属性是一个数组 [complexkey1,complexkey2]，对应的值也是一个数组 [complexvalue1,complexvalue2]

#### 复合结构

数组和对象可以构成复合结构，例：

```yaml
languages:
  - Ruby
  - Perl
  - Python 
websites:
  YAML: yaml.org 
  Ruby: ruby-lang.org 
  Python: python.org 
  Perl: use.perl.org
```

转换为 json 为：

```json
{ 
  languages: [ 'Ruby', 'Perl', 'Python'],
  websites: {
    YAML: 'yaml.org',
    Ruby: 'ruby-lang.org',
    Python: 'python.org',
    Perl: 'use.perl.org' 
  } 
}
```
