# 函数介绍

`rsaEncryptPKCS1v15`函数主要是用来将指定数据按照PKCS1v15的填充模式进行RSA加密运算
`rsaDecryptPKCS1v15`函数主要是用来将指定数据按照PKCS1v15的填充模式进行RSA解密运算

## 函数原型

`func rsaEncryptPKCS1v15(b1 bytes, k1 string)bytes`
`func rsaDecryptPKCS1v15(b1 bytes, k1 string)bytes`

### 参数介绍

| 参数名称 | 参数介绍    |
|------|---------|
| b1   | 待加解密的数据 |
| k1   | 公/私钥    |

## 举例

privateKey
```
-----BEGIN RSA PRIVATE KEY-----
MIICXQIBAAKBgQCuZxusoOKrN9RvDncGtS47IoIO51ctB03RTgUmKUOkceI7N4C+
GPoqc2kZHIed/+oeY0nuvpDrFGTFF6TogQR6U0EYo3IMsbJ3inGEUBvNHR86SLbj
5ph1TGITWKLvqw6R+TqfjBeLOckpLMf5t8oZl2ax3Aq6lgWaGlr5qb5e8QIDAQAB
AoGABWrFNqlYJQySxADRZGNSDGrr2oHnyKmkioLngMoRFG1noyJ8FJuCwLkNO4cR
9M/HQjqgCCMB7hVX9HHBsOmZ/ZQJZstzlmMIPKVtOzEesZnOPiqR9zvqiX452ZuX
z3SHp5Te6lBw6iWyE8mcIaPoQacy/9slJOV9bUQNeYvnkykCQQDEcf44B0gYswvu
AqcDChp4vJIFqb0qX9gVZZFIw0m9FFOibQ2JwY9mAN6v9GbHAbLW/il+KoiMlRcH
J8Y311f5AkEA40ZoXK/Aesy/hP6HvXs+2Ul+PAYOBcW2YX9JrnudpkfSx5wrrKIW
ud0DFGCZFQvNFB0h9tjRS4BbZnxVZkksuQJBAJYZ9AGjrrcQuCDY7fwokCmJDJo/
JEdojJds0CIk9gb/rRgC88E6oPNz3rPbr1yIM7qK4fGBVmz0zm+tOIwagyECQQCL
K3sOfqSrzaLdSotOUSDcJ2/AS6jcigQzUaGJ0bJotwRwLMZlsN+fsqGHIdu7kn1i
+q/omz4WMKRHbo1Q1DApAkBjlOreZ7CFFy02EXLu2YDx73KucwTTYjoZfMpz/9G9
4+ZJ0E6Kx6JrjVKaPfJ40vWreWAula086Ke872WPCbhA
-----END RSA PRIVATE KEY-----
```

publicKey
```
-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCuZxusoOKrN9RvDncGtS47IoIO
51ctB03RTgUmKUOkceI7N4C+GPoqc2kZHIed/+oeY0nuvpDrFGTFF6TogQR6U0EY
o3IMsbJ3inGEUBvNHR86SLbj5ph1TGITWKLvqw6R+TqfjBeLOckpLMf5t8oZl2ax
3Aq6lgWaGlr5qb5e8QIDAQAB
-----END PUBLIC KEY-----
```

```yaml
name: test-rsa
transport: http
set:
  privateKey: string("-----BEGIN RSA PRIVATE KEY-----\nMIICXQIBAAKBgQCuZxusoOKrN9RvDncGtS47IoIO51ctB03RTgUmKUOkceI7N4C+\nGPoqc2kZHIed/+oeY0nuvpDrFGTFF6TogQR6U0EYo3IMsbJ3inGEUBvNHR86SLbj\n5ph1TGITWKLvqw6R+TqfjBeLOckpLMf5t8oZl2ax3Aq6lgWaGlr5qb5e8QIDAQAB\nAoGABWrFNqlYJQySxADRZGNSDGrr2oHnyKmkioLngMoRFG1noyJ8FJuCwLkNO4cR\n9M/HQjqgCCMB7hVX9HHBsOmZ/ZQJZstzlmMIPKVtOzEesZnOPiqR9zvqiX452ZuX\nz3SHp5Te6lBw6iWyE8mcIaPoQacy/9slJOV9bUQNeYvnkykCQQDEcf44B0gYswvu\nAqcDChp4vJIFqb0qX9gVZZFIw0m9FFOibQ2JwY9mAN6v9GbHAbLW/il+KoiMlRcH\nJ8Y311f5AkEA40ZoXK/Aesy/hP6HvXs+2Ul+PAYOBcW2YX9JrnudpkfSx5wrrKIW\nud0DFGCZFQvNFB0h9tjRS4BbZnxVZkksuQJBAJYZ9AGjrrcQuCDY7fwokCmJDJo/\nJEdojJds0CIk9gb/rRgC88E6oPNz3rPbr1yIM7qK4fGBVmz0zm+tOIwagyECQQCL\nK3sOfqSrzaLdSotOUSDcJ2/AS6jcigQzUaGJ0bJotwRwLMZlsN+fsqGHIdu7kn1i\n+q/omz4WMKRHbo1Q1DApAkBjlOreZ7CFFy02EXLu2YDx73KucwTTYjoZfMpz/9G9\n4+ZJ0E6Kx6JrjVKaPfJ40vWreWAula086Ke872WPCbhA\n-----END RSA PRIVATE KEY-----")
  publicKey: string("-----BEGIN PUBLIC KEY-----\nMIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCuZxusoOKrN9RvDncGtS47IoIO\n51ctB03RTgUmKUOkceI7N4C+GPoqc2kZHIed/+oeY0nuvpDrFGTFF6TogQR6U0EY\no3IMsbJ3inGEUBvNHR86SLbj5ph1TGITWKLvqw6R+TqfjBeLOckpLMf5t8oZl2ax\n3Aq6lgWaGlr5qb5e8QIDAQAB\n-----END PUBLIC KEY-----")
  password: randomLowercase(8)
rules:
  r0:
    request:
      method: GET
      path: /
    expression: bytes(password) == rsaDecryptPKCS1v15(rsaEncryptPKCS1v15(bytes(password), publicKey), privateKey)
expression: r0()
detail:
  author: test
```
