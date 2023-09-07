# 函数介绍

`aesDecrypt`函数主要是用来对aes的数据进行解密

## 函数原型

`func aesDecrypt(d1 bytes/string, k1 bytes/string, m1 string)string`

### 参数介绍

| 参数名称 | 参数介绍                                 |
|------|--------------------------------------|
| d1   | 待解密数据                                |
| k1   | 用于解密的密钥                              |
| m1   | 加密模式，可以是"ECB"（电子密码本模式）或"CBC"（密码块链模式） |

## 举例

| 待转换数据                              | 转换语句                                                                                  | 输出结果   |
|------------------------------------|---------------------------------------------------------------------------------------|--------|
| `580e8510e7fcee94ea389634359508a9` | `aesDecrypt(hexDecode("580e8510e7fcee94ea389634359508a9"),b"asdfghjklmnbvcxz","ECB")` | `test` |
