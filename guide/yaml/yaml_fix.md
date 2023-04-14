# YAML插件修复手册

## 简介

lint是一项用于检验脚本的可用性、功能性和标准性的组件。

它将评估提交的yaml脚本，并提示出脚本中的错误和警告。

错误项需要修改并解决错误，警告项根据您的实际情况决定是否需要修复。

## 类型

### filename

#### 描述

filename的检查主要是检查文件名和name的规范性。

提示等级为error

#### 修复方式

- **报错信息**：`yaml script name should start with poc- or fingerprint-`
- **修复方式**：将name的值改为poc-或fingerprint-开头
  <br /><br />
- **报错信息**：`filename's extension must be .yml`
- **修复方式**：将文件名称改为以.yml结尾
  <br /><br />
- **报错信息**：`invalid file name pattern`
- **修复方式**：文件名必须符合`^[a-z0-9\-]+\.yml$`这样一个正则表达式
  <br /><br />
- **报错信息**：`filename xx and poc name xx do not match, maybe it should be xx`
- **修复方式**：文件名称和name的值不匹配，将文件名称改为name去掉”poc-yaml“的值，比如name：poc-yaml-test，那么文件名就应该是test.yml

#### 跳过方式

此处的跳过方式仅支持跳过文件名称跟name的匹配检测，也就是上面的最后一个报错信息的检查，其他的检查不支持跳过

`name: poc-yaml-test   # nolint[:namematch]`

通过在name字段的后面添加`# nolint[:namematch]`即可跳过。

### yamlschema

#### 描述

yamlschema的检查主要是检查yaml是否符合gamma既定的规则 [规则链接](https://github.com/chaitin/gamma/blob/master/static/schema/schema-v2.json)

提示等级为error

#### 修复方式

- **报错信息**：`jsonschema: 'XXX' does not validate with map:///script_schema.json#/$ref/properties/rules/additionalProperties/$ref/additionalProperties: additionalProperties 'XX', 'XXXX', 'XXX' not allowed`
- **修复方式**：根据jsonschema提示的内容，在指定位置补充或更正对应属性。
  <br /><br />
- **报错信息**：`jsonschema: 'XXX' does not validate with map:///script_schema.json#/UnevaluatedProperties: not allowed`
- **修复方式**：根据jsonschema提示的内容，在指定位置移除或更正对应属性。
  <br /><br />

#### 跳过方式

暂不支持跳过

### yamllint

#### 描述

yamllint的检查主要是检查脚本是否符合既定的yaml语法规则

提示等级为error

#### 修复方式

- **报错信息**：`yamllint not found, please install it manually. https://yamllint.readthedocs.io/en/stable/quickstart.html#installing-yamllint`
- **修复方式**：本地需安装yamllint工具。
  <br /><br />
- **报错信息**：[/var/folders/g7/w1h62nqd0tsd3nm0pxks0ckc0000gn/T/293558634 20:1 error too many blank lines (3 > 0)  (empty-lines)]
- **修复方式**：根据yamllint提示的内容，修复yaml。
  <br /><br />

#### 跳过方式

通过在需要跳过的行后或者行上方添加`# yamllint disable-line rule:[rule name]`即可跳过。[参考链接](https://yamllint.readthedocs.io/en/stable/disable_with_comments.html#disabling-checks-for-a-specific-line)

`key:  value # yamllint disable-line rule:key-trailing-spaces`

如上即可跳过yamllint的trailing-spaces规则

### bytecheck

#### 描述

bytecheck的检查主要是检查字节/编码是否规范

提示等级为warn

#### 修复方式

- **报错信息**：```bytes warnning in: : response.body.bcontains(b"\xe4\xb8\xad\xe6\x96\x87") bytes warnning in: b: b"\xe4\xb8\xad\xe6\x96\x872" bytes warnning in: pb: b"\xe4\xb8\xad\xe6\x96\x874" bytes warnning in: : "中文".bmatches(response.body) bys warnning in: str: "中文1" bytes warnning in: pstr: "中文3" bytes warnning in: str: "中文1"```
- **修复方式**：根据bytecheck提示的内容，可知yaml中使用了中文编码。
  <br /><br />

#### 跳过方式

```
str: r"中文"

result: str.bsubmatch(response.body) # nolint[:bytelint]
```

通过在使用变量的行后面添加`# nolint[:bytelint]`即可跳过。

### cellint

#### 描述

cellint的检查主要是检查脚本中使用到的cel语句的语法

提示等级为error

#### 修复方式

- **报错信息**：
  ```
  Current:response.body.bcontains(b'\xb3\xc9\xb9\xa6\xb8\xfc\xb8\xc4\xbb\xf2\xbb\xd8\xb8\xb4\xd2\xbb\xcc\xf5\xc1\xf4\xd1\xd4')
  Expected:response.body.bcontains(b"\xb3\xc9\xb9\xa6\xb8\xfc\xb8\xc4\xbb\xf2\xbb\xd8\xb8\xb4\xd2\xbb\xcc\xf5\xc1\xf4\xd1\xd4")]
  ```
- **修复方式**：根据cellint中Expected提示的内容，''需修改为""。
  <br /><br />
- **报错信息**：
  ```
  Current:response.status == 200   && response.body.bcontains(bytes(fileContent))
  Expected:response.status == 200 && response.body.bcontains(bytes(fileContent))
  ```
- **修复方式**：根据cellint中Expected提示的内容，cel语句中出现了多余的空格。
  <br /><br />
- **报错信息**：
  ```
  Current:(response.status == 200)
  Expected:response.status == 200
  ```
- **修复方式**：根据cellint中Expected提示的内容，cel语句中出现了多余的`()`。
  <br /><br />

#### 跳过方式

```
expression: response.body.bcontains(b'\xb3\xc9\xb9\xa6\xb8\xfc\xb8\xc4\xbb\xf2\xbb\xd8\xb8\xb4\xd2\xbb\xcc\xf5\xc1\xf4\xd1\xd4') # nolint[:cellint]
```

通过在使用cel语句的行后面添加`# nolint[:cellint]`即可跳过。

### transportcheck

#### 描述

transportcheck的检查主要是检查脚本中的transport和request是否合规

提示等级为error

#### 修复方式

- **报错信息**：`request has body but not set content type`
- **修复方式**：request存在body时，其headers中一定要有Content-Type。
  <br /><br />

#### 跳过方式

`transport: http # nolint[:transportlint]`

通过在transport后面添加`# nolint[:transportlint]`即可跳过。

### unusedlint

#### 描述

unusedlint的检查主要是检查脚本中是否存在未使用的变量

提示等级为error

#### 修复方式

- **报错信息**：`can not find where to use variable: sss`
- **修复方式**：移除掉未使用的变量。
  <br /><br />

#### 跳过方式

`build_id: search["build_id"] # nolint[:unusedlint]`

通过在定义的变量的行后面添加`# nolint[:unusedlint]`即可跳过。

### regularlint

#### 描述

regularlint的检查主要是检查正则参数是否有误，例如参数是否手误写反

提示等级为warn

#### 修复方式

- **报错信息**：`regular function parameter is not defined: body_string`
- **修复方式**：正则函数的第一个参数的值应该可推导。
  <br /><br />

#### 跳过方式

`result3: response.body_string.bsubmatch(str) # nolint[:regularlint]`

通过在使用正则函数的行后面添加`# nolint[:regularlint]`即可跳过。

### pathlint

#### 描述

pathlint的检查主要是检查脚本中path是否规范 (常规lint不会检测)

提示等级为warn

#### 修复方式

- **报错信息**：`randomFilename used in path. Do you need get404path?`
- **修复方式**：使用随机数组合path，可以考虑替换成指定的get404path。
  <br /><br />

#### 跳过方式

```
path: /{{randomFilename}}?action=upload_file&filename=../.{{randomFilename}}.jsp # nolint[:pathlint]
```

通过在path所在行后面添加`# nolint[:pathlint]`即可跳过。
