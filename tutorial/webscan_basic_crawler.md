# 使用 xray 基础爬虫模式进行漏洞扫描

爬虫模式是模拟人工去点击网页的链接，然后去分析扫描，和代理模式不同的是，爬虫不需要人工的介入，访问速度要快很多，但是也有一些缺点需要注意

 - xray 的基础爬虫不能处理 js 渲染的页面，如果需要此功能，请参考 [版本对比](/generic/compare)

## 启动爬虫


<!-- tabs:start -->

#### ** Windows **

```
./xray_windows_amd64 webscan --basic-crawler http://testphp.vulnweb.com/ --html-output xray-crawler-testphp.html
```


#### ** MacOS **

```
./xray_darwin_amd64 webscan --basic-crawler http://testphp.vulnweb.com/ --html-output xray-crawler-testphp.html
```

#### ** Linux **

```
./xray_linux_amd64 webscan --basic-crawler http://testphp.vulnweb.com/ --html-output xray-crawler-testphp.html
```

<!-- tabs:end -->

## 登录后的网站扫描

如果用的是代理模式，只要浏览器是登录状态，那么漏洞扫描收到的请求也都是登录状态的请求。但对于普通爬虫而言，就没有这么“自动化”了，
但是可以通过配置 Cookie 的方式实现登录后的扫描。

打开配置文件，修改 `http` 配置部分的 `Headers` 项:

```
http:
  headers:
    Cookie: key=value
```

上述配置将为所有请求（包括爬虫和漏洞扫描）增加一条 Cookie `key=value`