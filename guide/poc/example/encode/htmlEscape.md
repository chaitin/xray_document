# 函数介绍

`htmlEscape`函数主要是用来将所给字符串或者字节数组进行 html 实体编码，并输出编码后的字符串

## 函数原型

- `func htmlEscape(s1 string, mode string, isAll bool) string`
- `func htmlEscape(b1 bytes, mode string, isAll bool) string`

### 参数介绍

| 参数名称 | 参数介绍                                                   |
| -------- | ---------------------------------------------------------- |
| `s1/b1`  | 为需要进行编码的字符串或者字节数组                         |
| `mode`   | html 实体编码的格式，支持`named`, `numeric`, `hex`三种模式 |
| `isAll`  | 是否进行全字符编码                                         |

> named模式支持将符号等特殊字符编码为特定的名称，numeric会将字符编码为10进制模式，hex会将字符编码为16进制模式

## 举例

| 待转换数据                                          | 转换语句                                                                         | 输出结果                                                                                                                                                                                                                                                                   |
| --------------------------------------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `"\"Fran & Freddie's Diner\" <tasty@example.com>"`  | `htmlEscape("\"Fran & Freddie's Diner\" <tasty@example.com>", "named", false)`   | `&quot;Fran &amp; Freddie&apos;s Diner&quot; &lt;tasty&commat;example&period;com&gt;`                                                                                                                                                                                      |
| `b"\"Fran & Freddie's Diner\" <tasty@example.com>"` | `htmlEscape("\"Fran & Freddie's Diner\" <tasty@example.com>", "named", true)`    | `&quot;&#70;&#114;&#97;&#110;&#32;&amp;&#32;&#70;&#114;&#101;&#100;&#100;&#105;&#101;&apos;&#115;&#32;&#68;&#105;&#110;&#101;&#114;&quot;&#32;&lt;&#116;&#97;&#115;&#116;&#121;&commat;&#101;&#120;&#97;&#109;&#112;&#108;&#101;&period;&#99;&#111;&#109;&gt;`             |
| `"\"Fran & Freddie's Diner\" <tasty@example.com>"`  | `htmlEscape("\"Fran & Freddie's Diner\" <tasty@example.com>", "numeric", true)`  | `&#34;&#70;&#114;&#97;&#110;&#32;&#38;&#32;&#70;&#114;&#101;&#100;&#100;&#105;&#101;&#39;&#115;&#32;&#68;&#105;&#110;&#101;&#114;&#34;&#32;&#60;&#116;&#97;&#115;&#116;&#121;&#64;&#101;&#120;&#97;&#109;&#112;&#108;&#101;&#46;&#99;&#111;&#109;&#62;`                    |
| `"\"Fran & Freddie's Diner\" <tasty@example.com>"`  | `htmlEscape("\"Fran & Freddie's Diner\" <tasty@example.com>", "numeric", false)` | `&#34;Fran &#38; Freddie&#39;s Diner&#34; &#60;tasty&#64;example&#46;com&#62;`                                                                                                                                                                                             |
| `"\"Fran & Freddie's Diner\" <tasty@example.com>"`  | `htmlEscape("\"Fran & Freddie's Diner\" <tasty@example.com>`                     | `&#x22;&#x46;&#x72;&#x61;&#x6E;&#x20;&#x26;&#x20;&#x46;&#x72;&#x65;&#x64;&#x64;&#x69;&#x65;&#x27;&#x73;&#x20;&#x44;&#x69;&#x6E;&#x65;&#x72;&#x22;&#x20;&#x3C;&#x74;&#x61;&#x73;&#x74;&#x79;&#x40;&#x65;&#x78;&#x61;&#x6D;&#x70;&#x6C;&#x65;&#x2E;&#x63;&#x6F;&#x6D;&#x3E;` |
| `"\|\\/,asd.{55r]("`                                | `htmlEscape("\|\\/,asd.{55r](", "named", false)`                                 | `&verbar;&bsol;&sol;&comma;asd&period;&lcub;55r&rsqb;&lpar;`                                                                                                                                                                                                               |
