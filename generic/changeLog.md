# XRAY Changelog
国内用户可以从 https://stack.chaitin.com/tool/detail?id=1 进行下载，速度更快

## 1.9.4(2023-01-10)

### 更新内容

#### 插件更新

1. 添加XStream扫描插件，支持列表如下（该插件需开启反连平台）
   - CVE-2021-21344
   - CVE-2021-21345
   - CVE-2021-39141
   - CVE-2021-39144
   - ...(共29个插件)
2. fastjson插件支持cve-2022-25845的检测

#### POC编写/执行更新

1. 新增警告信息，师傅们可以根据警告信息删除检测插件创建的文件等
2. 支持在GET，HEAD，OPTION时添加body
3. 添加[compare version](guide/poc/func?id=版本)函数，可以对匹配出的版本进行对比
4. 添加[html实体编码](guide/poc/example/encode/htmlEscape)/[解码](guide/poc/example/encode/htmlUnescape)函数
5. 添加[java反序列化函数](guide/poc/example/ysoGadget/javaGadget)
6. 添加[hex](guide/poc/example/encode/hex)/[hexDecode](guide/poc/example/encode/hexDecode)函数

### 优化内容

1. 优化了反连平台漏洞捕获逻辑，提高了命中率
2. 优化了 poc lint 变得更人性化
3. yaml脚本支持获取rmi反连平台的链接，具体使用请参考官方文档
4. 优化了Struts2检测模块，添加反连确认，减少误报漏报

### 修复POC
#### 规则优化，规则弱
```
poc-yaml-drawio-cve-2022-1713-ssrf
poc-yaml-h3c-cvm-upload-file-upload
poc-yaml-iis-cve-2017-7269
poc-yaml-74cms-sqli-cve-2020-22209
poc-yaml-reporter-file-read
poc-yaml-wanhu-ezoffice-documentedit-sqli
poc-yaml-joomla-cve-2017-8917-sqli
poc-yaml-iis-cve-2017-7269
poc-yaml-emerge-e3-cve-2019-7256
poc-yaml-alibaba-nacos-v1-auth-bypass
poc-yaml-wanhu-ezoffice-documentedit-sqli
poc-yaml-magicflow-gateway-main-xp-file-read
poc-yaml-gitblit-cve-2022-31268
poc-yaml-phpstudy-nginx-wrong-resolve
poc-yaml-confluence-cve-2022-26138
poc-yaml-metinfo-lfi-cnvd-2018-13393
poc-yaml-zabbix-cve-2019-17382
poc-yaml-wordpress-paypal-pro-cve-2020-14092-sqli
poc-yaml-vite-cnvd-2022-44615
poc-yaml-phpmyadmin-cve-2018-12613-file-inclusion
poc-yaml-zabbix-cve-2022-23134
poc-yaml-ametys-cms-cve-2022-26159
```

#### 优化删除（功能与xray的通用插件重复）
```
poc-yaml-nexusdb-cve-2020-24571-path-traversal
poc-yaml-specoweb-cve-2021-32572-fileread
poc-yaml-tvt-nvms-1000-file-read-cve-2019-20085
poc-yaml-zyxel-vmg1312-b10d-cve-2018-19326-path-traversal
```

#### 新增无害化处理
```
poc-yaml-fanruan-v9-file-upload
poc-yaml-h3c-cvm-upload-file-upload
poc-yaml-seeyon-unauthorized-fileupload
poc-yaml-thinkcmf-write-shell
poc-yaml-wanhu-oa-officeserver-file-upload
poc-yaml-weaver-oa-workrelate-file-upload
poc-yaml-yonyou-grp-u8-file-upload
poc-yaml-yonyou-nc-file-accept-upload
poc-yaml-yonyou-u8c-file-upload
poc-yaml-zhiyuan-oa-wpsassistservlet-file-upload
```

### 新增POC 96个
```
poc-yaml-ruijie-fileupload-fileupload-rce
poc-yaml-eweaver-oa-mecadminaction-sqlexec
poc-yaml-xxl-job-default-password
poc-yaml-wordpress-plugin-superstorefinder-ssf-social-action-php-sqli
poc-yaml-magento-config-disclosure-info-leak
poc-yaml-ukefu-cnvd-2021-18305-file-read
poc-yaml-ukefu-cnvd-2021-18303-ssrf
poc-yaml-eweaver-eoffice-mainselect-info-leak
poc-yaml-linksys-cnvd-2014-01260
poc-yaml-wordpress-welcart-ecommerce-cve-2022-41840-path-traversal
poc-yaml-jeesite-userfiles-path-traversal
poc-yaml-yongyou-nc-iupdateservice-xxe
poc-yaml-v-sol-olt-platform-unauth-config-download
poc-yaml-ibm-websphere-portal-hcl-cve-2021-27748-ssrf
poc-yaml-yonyou-nc-uapws-db-info-leak
poc-yaml-yonyou-nc-service-info-leak
poc-yaml-yongyou-nc-cloud-fs-sqli
poc-yaml-finecms-filedownload
poc-yaml-weaver-eoffice-userselect-unauth
poc-yaml-fortinet-cve-2022-40684-auth-bypass
poc-yaml-dapr-dashboard-cve-2022-38817-unauth
poc-yaml-wordpress-zephyr-project-manager-cve-2022-2840-sqli
poc-yaml-jira-cve-2022-39960-unauth
poc-yaml-qnap-cve-2022-27593-fileupload
poc-yaml-wordpress-all-in-one-video-gallery-cve-2022-2633-lfi
poc-yaml-atlassian-bitbucket-archive-cve-2022-36804-remote-command-exec
poc-yaml-wordpress-simply-schedule-appointments-cve-2022-2373-unauth
poc-yaml-zoho-manageengine-opmanager-cve-2022-36923
poc-yaml-red-hat-freeipa-cve-2022-2414-xxe
poc-yaml-wavlink-cve-2022-2488-rce
poc-yaml-wavlink-cve-2022-34045-info-leak
poc-yaml-wordpress-shareaholic-cve-2022-0594-info-leak
poc-yaml-wordpress-wp-stats-manager-cve-2022-33965-sqli
poc-yaml-opencart-newsletter-custom-popup-sqli
poc-yaml-wordpress-events-made-easy-cve-2022-1905-sqli
poc-yaml-wordpress-kivicare-cve-2022-0786-sqli
poc-yaml-wordpress-cve-2022-1609-rce
poc-yaml-solarview-compact-cve-2022-29303-rce
poc-yaml-wordpress-arprice-lite-cve-2022-0867-sqli
poc-yaml-wordpress-fusion-cve-2022-1386-ssrf
poc-yaml-wordpress-nirweb-cve-2022-0781-sqli
poc-yaml-wordpress-metform-cve-2022-1442-info-leak
poc-yaml-wordpress-mapsvg-cve-2022-0592-sqli
poc-yaml-wordpress-badgeos-cve-2022-0817-sqli
poc-yaml-wordpress-daily-prayer-time-cve-2022-0785-sqli
poc-yaml-wordpress-woo-product-table-cve-2022-1020-rce
poc-yaml-wordpress-documentor-cve-2022-0773-sqli
poc-yaml-wordpress-multiple-shipping-address-woocommerce-cve-2022-0783-sqli
poc-yaml-gitlab-cve-2022-1162-hardcoded-password
poc-yaml-thinkphp-cve-2022-25481-info-leak
poc-yaml-wordpress-cve-2022-0591-ssrf
poc-yaml-wordpress-simple-link-directory-cve-2022-0760-sqli
poc-yaml-wordpress-ti-woocommerce-wishlist-cve-2022-0412-sqli
poc-yaml-wordpress-notificationx-cve-2022-0349-sqli
poc-yaml-wordpress-page-views-count-cve-2022-0434-sqli
poc-yaml-wordpress-masterstudy-lms-cve-2022-0441-unauth
poc-yaml-wordpress-seo-cve-2021-25118-info-leak
poc-yaml-wordpress-perfect-survey-cve-2021-24762-sqli
poc-yaml-wordpress-asgaros-forum-cve-2021-24827-sqli
poc-yaml-tcexam-cve-2021-20114-info-leak
poc-yaml-wordpress-woocommerce-cve-2021-32789-sqli
poc-yaml-wordpress-profilepress-cve-2021-34621-unauth
poc-yaml-wordpress-wp-statistics-cve-2021-24340-sqli
poc-yaml-voipmonitor-cve-2021-30461-rce
poc-yaml-rocket-chat-cve-2021-22911-nosqli
poc-yaml-pega-infinity-cve-2021-27651-unauth
poc-yaml-wordpress-modern-events-calendar-lite-cve-2021-24146-info-leak
poc-yaml-afterlogic-webmail-cve-2021-26294-path-traversal
poc-yaml-wavlink-cve-2020-13117-rce
poc-yaml-prestashop-cve-2021-3110-sqli
poc-yaml-cockpit-cve-2020-35847-nosqli
poc-yaml-cockpit-cve-2020-35848-nosqli
poc-yaml-keycloak-cve-2020-10770-ssrf
poc-yaml-prestashop-cve-2020-26248-sqli
poc-yaml-wordpress-paypal-pro-cve-2020-14092-sqli
poc-yaml-microstrategy-cve-2020-11450-info-leak
poc-yaml-adobe-experience-manager-cve-2019-8086-xxe
poc-yaml-blogengine-net-cve-2019-10717-path-traversal
poc-yaml-dotcms-cve-2018-17422-url-redirection
poc-yaml-php-proxy-cve-2018-19458-fileread
poc-yaml-circarlife-scada-cve-2018-16671-info-leak
poc-yaml-circarlife-scada-cve-2018-16670-info-leak
poc-yaml-circarlife-scada-cve-2018-16668-info-leak
poc-yaml-dotnetnuke-cve-2017-0929-ssrf
poc-yaml-orchid-core-vms-cve-2018-10956-path-traversal
poc-yaml-circarlife-scada-cve-2018-12634-info-leak
poc-yaml-nuuo-nvrmini2-cve-2018-11523-upload
poc-yaml-jolokia-cve-2018-1000130-code-injection
poc-yaml-fiberhome-cve-2017-15647-path-traversal
poc-yaml-opendreambox-cve-2017-14135-rce
poc-yaml-sap-cve-2017-12637-fileread
poc-yaml-glassfish-cve-2017-1000029-lfi
poc-yaml-boa-cve-2017-9833-fileread
poc-yaml-mantisbt-cve-2017-7615-unauth
poc-yaml-wordpress-cve-2017-5487-info-leak
poc-yaml-thinkcmf-cve-2018-19898-sqli
```

## 1.9.3(2022-10-13)
### 更新内容

1. 优化扫描效率
2. 增强子域名收集功能
3. 修复一些意外导致panic的问题
4. 添加 rev 字符串反向函数，添加 upper 字符串大写函数
5. 为 response 添加 icon_url 属性
6. 添加 getIconContent 方法
7. 新增如下插件：
    - amcrest-cve-2017-8229-info-leak.yml
    - anymacro-mail-fileread.yml
    - anymacro-mail-sql-injection.yml
    - aspcms-sqli.yml
    - changjietong-downloadproxy-file-read.yml
    - cisco-router-cve-2019-1653-info-leak.yml
    - cisco-rv-series-router-cve-2021-1472-rce.yml
    - cisco-rv132w-router-cve-2018-0127-information-disclosure.yml
    - cockpit-cve-2020-35846-sqli.yml
    - discuz-cve-2019-13956-rce.yml
    - dlink-cve-2021-42627-unauth.yml
    - e-cology-loginsso-sqli.yml
    - ecology-hrmcareerapplyperview-sql.yml
    - fanweioa-signaturedownload-file-read.yml
    - fumengyun-ajaxmethod-name-sqli.yml
    - h3c-cvm-upload-file-upload.yml
    - h3c-er3100-unauth-access.yml
    - huawei-dg8045-deviceinfo-info-leak.yml
    - inspur-clusterenginev4-sysshell-remote-command-exec.yml
    - kavita-cover-upload-file-read.yml
    - kingdee-k3-cloud-mobfileupload-upload.yml
    - kkfileview-getcorsfile-ssrf.yml
    - kkfileview-xss-cve-2022-35151.yml
    - landray-oa-datajson-rce.yml
    - lg-n1a1-nas-cnnvd-201607-467-rce.yml
    - lucee-cve-2021-21307-rce.yml
    - manageengine-servicedesk-cve-2017-11512-lfi.yml
    - netgear-cnnvd-201306-024.yml
    - nuuo-nvrmini-cve-2018-14933.yml
    - oracle-ebs-bispgrapgh-file-read.yml
    - qiwang-erp-sql-injection.yml
    - reporter-file-read.yml
    - sanfor-reporter-anyfileread.yml
    - solarview-compact-rce-cve-2022-29298.yml
    - telesquare-cve-2021-46422-rce.yml
    - tongda-anywhere2017-auth-bypass.yml
    - tongda-oa-file-read.yml
    - tongda-oa-get-contactlist-php-leak-information.yml
    - tongda-oa-v11-auth-mobi-php-get-user-session.yml
    - topapp-lb-any-user-login.yml
    - totolink-cve-2022-25076-rce.yml
    - u5cms-cve-2022-32444-url-redirection.yml
    - unraid-cve-2020-5847-remote-code-execution.yml
    - vicidial-info-leak.yml
    - wanhu-ezoffice-documentedit-sqli.yml
    - wanhu-ezoffice-downloadservlet-path-traversal.yml
    - wavlink-cve-2022-2486-rce.yml
    - wavlink-cve-2022-31845.yml
    - wavlink-cve-2022-31846.yml
    - wavlink-cve-2022-34046.yml
    - wavlink-cve-2022-34049.yml
    - wavlink-cve-2022-34570-info-leak.yml
    - wavlink-nightled-remote-command-execute.yml
    - wavlink-password-disclosure-cve-2022-34047.yml
    - weaver-oa-filedownload-jsp-path-traversal.yml
    - weaver-oa-jqueryfiletree-jsp-path-traversal.yml
    - weaver-oa-sptmforportalthumbnail-jsp-path-traversal.yml
    - weaver-oa-ultra-vires.yml
    - weblogic-local-cve-2022-21371-file-inclusion.yml
    - wordpress-wp-google-maps-cve-2019-10692-sqli.yml
    - yongyou-chanjet-sql-injection.yml
    - yonyou-fe-templateoftaohong-manager-jsp-directory-traversal.yml
    - yonyou-iufo-userinformation-disclosure.yml
    - zimbra-cve-2022-27925.yml
    - zyxel-cve-2022-0342-auth-bypass.yml
    - zyxel-vmg1312-b10d-cve-2018-19326-path-traversal.yml
    - zyxel-ztp-rce-cve-2022-30525.yml
    - tongda-oa-unauth.yml
    - 74cms-cve-2020-22211-sqli.yml
    - alibaba-nacos-cve-2021-29442-unauth.yml
    - artica-pandora-fms-cve-2020-8497-unauth.yml
    - bitbucket-unauth.yml
    - citrix-cve-2020-8194-code-injection.yml
    - dahua-dss-file-read.yml
    - drawio-cve-2022-1713-ssrf.yml
    - dzzoffice-2-02-1-sc-utf8-unauth.yml
    - dzzoffice-2-02-1-sc-utf8-xss.yml
    - emerge-e3-series-cve-2022-31269.yml
    - eweaver-eoffice-ajax-upload.yml
    - glpi-barcode-cve-2021-43778-path-traversal.yml
    - glpi-telemetry-cve-2021-39211-info-leak.yml
    - gurock-testrail-cve-2021-40875-info-leak.yml
    - hwl-2511-ss-cve-2022-36554-rce.yml
    - intouch-access-anywhere-cve-2022-23854-lfi.yml
    - ispyconnect-cve-2022-29775-unauth.yml
    - kyocera-printer-cve-2020-23575-path-traversal.yml
    - laravel-filemanager-cve-2022-40734-path-traversal.yml
    - msnswitch-cve-2022-32429.yml
    - node-red-cve-2021-25864-fileread.yml
    - oracle-ebs-cve-2018-3167-ssrf.yml
    - panabit-syaddmount-command-exec.yml
    - prtg-network-monitor-cve-2020-11547-info-leak.yml
    - redash-cve-2021-41192-unauth.yml
    - springboot-jolokia-lfi.yml
    - tapestry-cve-2019-0195-readfile.yml
    - teampass-cve-2020-12478-unauth.yml
    - thinfinity-virtualui-cve-2021-44848-user-enum-unauth.yml
    - tongda-oa-gateway-path-traversal.yml
    - wapples-filrewall-cve-2022-35413.yml
    - wavlink-cve-2022-31847-info-leak.yml
    - weaver-e-cology-dbconfigreader-jsp-info-leak.yml
    - weaver-e-cology-ktreeuploadaction-upload.yml
    - weaver-e-mobile-client-do-sqli.yml
    - weaver-e-mobile-ognl-inject.yml
    - wordpress-backupbuddy-cve-2022-31474-lfi.yml
    - yonyou-u8c-file-upload.yml
    - zoho-manageengine-adaudit-plus-cve-2022-28219-xxe.yml
8. 对如下插件规则进行修复，增强：
    - full-read-ssrf-in-spring-cloud-netflix.yml
    - wuzhicms-v410-sqli.yml
    - nexusdb-cve-2020-24571-path-traversal.yml
    - joomla-cve-2015-7297-sqli.yml
    - joomla-cve-2017-8917-sqli.yml
    - zcms-v3-sqli.yml
    - 74cms-sqli.yml
    - discuz-v72-sqli.yml
    - duomicms-sqli.yml
    - seacms-sqli.yml
    - f5-big-ip-cve-2022-1388-rce.yml
    - nginx-cve-2017-7529-info-leak.yml
    - cerebro-request-ssrf.yml
    - openfire-cve-2019-18394-ssrf.yml
    - prometheus-url-redirection-cve-2021-29622.yml
    - zoho-cve-2022-23779-info-leak.yml
    - xunchi-cnvd-2020-23735-file-read.yml
    - 74cms-se-cve-2022-33095.yml
    - e-office-v10-sqli.yml
    - weaver-emobile-v6-sqli.yml
    - mingyu-waf-login-bypass.yml

### github更新内容

1. 做了一些优化
    1. 优化扫描效率
    2. 增强子域名收集功能
2. 增加了一些功能
    1. 添加burp的history导出文件转yml脚本的功能
    2. log4j2-rce的检测
    3. 为自定义脚本(gamma)添加
        1. 格式化时间戳函数
        2. 进制转换函数
        3. sha，hmacsha函数
        4. url全字符编码函数
        5. rev 字符串反向函数
        6. 添加 upper 字符串大写函数
        7. dir()
        8. basename()
        9. body_string
        10. title_string
    4. 扫描时，可以指定POC的危害等级，分为`low，medium，high，critical`，通过`--level`参数指定
    5. 为shiro插件添加文件加载功能，可以直接加载指定文件中的key
    6. 可在配置文件中配置每个poc的标签，通过--tags来指定标签扫描
    7. 更新了`--list`功能，可查看相关标签对应poc
    9. 为 response 添加 icon_url 属性
3. 修复了一些问题
    1. 修复cve-2021-29490误报严重问题
    2. 修复报告只显示参考链接，不显示提交者的问题
    3. 修复cache可能出现的请求不发送问题
    4. 过滤部分冗余的错误日志
    5. 修复一些意外导致panic的问题
4. 新增x命令
    1. 支持对发现的web站点进行漏洞探测
    2. 支持带宽控制与智能速率调节，最优化扫描效率
    3. 支持多目标多端口随机探测，基于有限元的随机化方案
    4. 支持ICMP/TCP/UDP主机存活探测
    5. 支持SYN/CONNECT端口扫描
    6. 支持URL/IP/域名/IP范围/CIDR等多种输入方式
    7. 支持指纹识别
- 该命令实际上是xray内置的、启用了

    - printer
    - service-scan
    - target-parse

这三个内置的插件的命令。

其中service-scan提供` 主机存活探测`、`服务指纹识别`、`web指纹识别` 的功能

可以查看 `plugin-config.xray.yaml`,`module-config.xray.yaml`获得详细配置信息，执行`xray x --help` 获取命令行参数与试用方法。

示例：
```bash=
xray x -t example.com
xray x -t http://example.com
xray x -t example.com/24
xray x -t 192.168.1.1/24
xray x -t 192.168.1.1-192.168.1.254
xray x -t 192.168.1.1-254
xray x -t 192.168.1.1-254 -p 22,80,443-445
```
5. 新增385个poc，感谢师傅们的提交，更新后即可自动加载
    - vmware-vcenter-cve-2021-21985-rce.yml
    - 74cms-cnvd-2021-45280.yml
    - adobe-coldfusion-cve-2018-15961.yml
    - ametys-cms-cve-2022-26159.yml
    - anmei-rce.yml
    - apache-airflow-cve-2020-13927-unauthorized.yml
    - apache-apisix-dashboard-api-unauth-rce.yml
    - atlassian-jira-unauth-user-enumeration.yml
    - auerswald-cve-2021-40859.yml
    - clickhouse-http-unauth.yml
    - cve-2022-24990-terramaster-fileupload.yml
    - dedecms-cve-2017-17731-sqli.yml
    - dedecms-mysql-error-trace.yml
    - dedecms-search-php-sqli.yml
    - doccms-sqli.yml
    - earcms-download-php-exec.yml
    - earcms-index-uplog-php-file-upload.yml
    - emlog-cve-2021-3293.yml
    - ewebs-fileread.yml
    - eyoucms-cve-2021-39501.yml
    - ezoffice-smartupload-jsp-upload.yml
    - finecms-getshell.yml
    - full-read-ssrf-in-spring-cloud-netflix.yml
    - grafana-snapshot-cve-2021-39226.yml
    - hadoop-yarn-rpc-rce.yml
    - hikvision-readfile.yml
    - hongfan-oa-readfile.yml
    - interlib-read-file.yml
    - ivanti-endpoint-manager-cve-2021-44529-rce.yml
    - jinhe-oa-readfile.yml
    - joomla-jck-cve-2018-17254-sqli.yml
    - kingdee-oa-apusic-readfile.yml
    - landray-oa-rce.yml
    - lionfish-cms-image-upload-php-upload.yml
    - lionfish-cms-wxapp-php-upload.yml
    - mastodon-cve-2022-0432.yml
    - metersphere-plugincontroller-rce.yml
    - metinfo-x-rewrite-url-sqli.yml
    - movabletype-cve-2021-20837-rce.yml
    - netpower-readfile.yml
    - nette-framework-cve-2020-15227-rce.yml
    - nginx-path-traversal.yml
    - oa8000-workflowservice-sqli.yml
    - onethink-sqli.yml
    - php-chat-live-uploadimg-html-upload.yml
    - phpcms-960-sqli.yml
    - phpweb-appplus-php-upload.yml
    - pigcms-file-upload.yml
    - prestashop-smartblog-cve-2021-37538.yml
    - qibocms-readfile.yml
    - rudloff-alltube-cve-2022-0692.yml
    - seeyon-oa-a6-information-disclosure.yml
    - spring-cloud-gateway-cve-2022-22947-rce.yml
    - supesite-sqli.yml
    - sysaid-itil-cve-2021-43972.yml
    - tongda-oa-action-upload-php-upload.yml
    - tongda-oa-report-bi-func-php-sqli.yml
    - voipmonitor-cve-2022-24260.yml
    - wanhuoa-upload-rce.yml
    - weaver-e-office-lazyuploadify-upload.yml
    - weaver-oa-eoffice-information-disclosure.yml
    - weijiaoyi-post-curl-ssrf.yml
    - western-digital-mycloud-ftp-download-exec.yml
    - western-digital-mycloud-jqueryfiletree-exec.yml
    - western-digital-mycloud-multi-uploadify-file-upload.yml
    - western-digital-mycloud-raid-cgi-exec.yml
    - western-digital-mycloud-sendlogtosupport-php-exec.yml
    - western-digital-mycloud-upload-php-exec.yml
    - western-digital-mycloud-upload-php-upload.yml
    - yonyou-erp-nc-readfile.yml
    - zhixiang-oa-sqli.yml
    - zoho-cve-2022-23779-info-leak.yml
    - adobe-coldfusion-cve-2021-21087.yml
    - alibaba-anyproxy-fetchbody-fileread.yml
    - apache-apisix-cve-2020-13945-rce.yml
    - apache-guacamole-default-password.yml
    - atlassian-jira-cve-2019-3403.yml
    - bsphp-unauthorized-access.yml
    - cve-2017-16894-sensitive-documents.yml
    - delta-entelitouch-cookie-user-password-disclosure.yml
    - domoticz-cve-2019-10664.yml
    - druid-cve-2021-25646.yml
    - dynamicweb-cve-2022-25369.yml
    - egroupware-spellchecker-rce.yml
    - elfinder-cve-2021-32682-rce.yml
    - emerge-e3-cve-2019-7256.yml
    - essl-dataapp-unauth-db-leak.yml
    - finecms-cve-2018-6893.yml
    - franklinfueling-cve-2021-46417-lfi.yml
    - fuelcms-cve-2018-16763-rce.yml
    - genixcms-register-cve-2015-3933-sqli.yml
    - getsimple-cve-2019-11231.yml
    - ghostscript-cve-2018-19475-rce.yml
    - jetty-servlets-concatservlet-information-disclosure-cve-2021-28169.yml
    - jetty-web-inf-information-disclosure-cve-2021-34429.yml
    - jira-cve-2021-26086.yml
    - joomla-history-cve-2015-7857-sqli.yml
    - jquery-picture-cut-upload-php-fileupload-cve-2018-9208.yml
    - jsrog-artifactory-cve-2019-9733.yml
    - kibana-cve-2019-7609-rce.yml
    - kodexplorer-directory-traversal.yml
    - maccms-cve-2017-17733-rce.yml
    - metabase-cve-2021-41277.yml
    - nostromo-cve-2011-0751-directory-traversal.yml
    - nuxeo-cve-2018-16341-rce.yml
    - odoo-cve-2019-14322.yml
    - php-imap-cve-2018-19518-rce.yml
    - phpmoadmin-cve-2015-2208-rce.yml
    - piwigo-cve-2022-26266-sqli.yml
    - rconfig-ajaxserversettingschk-cve-2019-16662-rce.yml
    - rconfig-commands-inc-cve-2020-10220-sqli.yml
    - resin-directory-traversal-cve-2021-44138.yml
    - ruanhong-jvm-lfi.yml
    - ruanhong-oa-xxe.yml
    - ruckus-default-password.yml
    - seeyon-oa-a8-m-information-disclosure.yml
    - showdoc-cnvd-2020-26585.yml
    - socomec-cve-2019-15859.yml
    - spring-data-rest-cve-2017-8046-rce.yml
    - subrions-search-cve-2017-11444-sqli.yml
    - teclib-glpl-cve-2019-10232.yml
    - terramaster-tos-cve-2022-24989.yml
    - tibco-jasperreports-cve-2018-18809-directory-traversal.yml
    - tongda-oa-login-code-php-login-bypass.yml
    - twonkyserver-cve-2018-7171-fileread.yml
    - vmware-workspace-cve-2021-22054-ssrf.yml
    - vmware-workspace-cve-2022-22954-rce.yml
    - vtigercrm-cve-2020-19363.yml
    - weaver-ecology-getsqldata-sqli-rce.yml
    - wordpress-site-editor-cve-2018-7422-lfi.yml
    - wso2-cve-2022-29464-fileupload.yml
    - wuzhicms-cve-2018-11528.yml
    - zabbix-cve-2019-17382.yml
    - zimbra-collaboration-server-cve-2013-7091-lfi.yml
    - zoneminder-cve-2016-10140-unauth-access.yml
    - apollo-default-password.yml
    - ecology-oa-eoffice-officeserver-php-file-read.yml
    - dptech-vpn-fileread.yml
    - ezoffice-filupload-controller-getshell.yml
    - yachtcontrol-webapplication-cve-2019-17270.yml
    - atlassian-jira-cve-2019-3401.yml
    - emerge-e3-cve-2019-7254.yml
    - vbulletin-cve-2020-12720.yml
    - netsweeper-webadmin-cve-2020-13167.yml
    - searchblox-cve-2020-35580.yml
    - opensis-cve-2020-6637.yml
    - hd-network-real-time-monitoring-system-cve-2021-45043.yml
    - visual-tools-dvr-vx16-cve-2021-42071.yml
    - jsrog-artifactory-cve-2019-17444.yml
    - reolink-RLC-410W-CVE-2022-21236.yml
    - tlr-2005ksh-cve-2021-45428.yml
    - zoho-manageengine-access-manager-plus-cve-2022-29081.yml
    - selea-ocr-anpr-arbitrary-get-file-read.yml
    - easyappointments-cve-2022-0482.yml
    - netgear-ssl-vpn-20211222-cve-2022-29383.yml
    - hitachi-vantara-pentaho-business-analytics-cve-2021-34684.yml
    - manageengine-opmanager-cve-2020-11946.yml
    - intelbras-wireless-cve-2021-3017.yml
    - sapido-router-unauthenticated-rce.yml
    - china-telecom-zte-f460-rce.yml
    - china-mobile-yu-router-information-disclosure.yml
    - tlr-2855ks6-arbitrary-file-creation-cve-2021-46418.yml
    - uniview-isc-rce.yml
    - feiyuxing-route-wifi-password-leak.yml
    - changjie-crm-sqli.yml
    - fhem-file-read-cve-2020-19360.yml
    - hikvision-ip-camera-backdoor.yml
    - kyocera-file-read.yml
    - niushop-cms-sqli.yml
    - dlink-dap-1620-firmware-cve-2021-46381.yml
    - emby-mediaserver-cve-2020-26948.yml
    - zoho-manageengine-opmanager-cve-2020-12116.yml
    - zabbix-cve-2022-23134.yml
    - tieline-ip-audio-gateway-cve-2021-35336.yml
    - selea-ocr-anpr-arbitrary-seleacamera-file-read.yml
    - microweber-cve-2022-0378.yml
    - atlassian-jira-cve-2022-0540.yml
    - sophosfirewall-bypass.yml
    - zoho-manageengine-desktop-central-cve-2021-44515.yml
    - tenda-11n-ultra-vires.yml
    - tenda-w15e-passsword-leak.yml
    - ziguang-sqli-cnvd-2021-41638.yml
    - kemai-ras-ultra-vires.yml
    - cerebro-request-ssrf.yml
    - motioneye-info-leak-cve-2022-25568.yml
    - yinda-get-file-read.yml
    - jupyter-notebook-rce.yml
    - e-message-unauth.yml
    - kkfileview-cve-2021-43734.yml
    - dlink-dsl-28881a-ultra-vires.yml
    - kunshi-vos3000-fileread.yml
    - reolink-nvr-configuration-disclosure-cve-2021-40150.yml
    - d-Link-dir-825-cve-2021-46442.yml
    - vite-cnvd-2022-44615.yml
    - gitblit-cve-2022-31268.yml
    - bigant-server-cve-2022-23347-lfi.yml
    - wordpress-page-builder-kingcomposer-cve-2022-0165-url-redirect.yml
    - huayu-reporter-rce.yml
    - d-link-dap-2020-cve-2021-27250.yml
    - 74cms-se-cve-2022-29720.yml
    - 74cms-se-cve-2022-33095.yml
    - pbootcms-rce-cve-2022-32417.yml
    - e-office-v10-sqli.yml
    - yonyou-nc-file-upload.yml
    - xiaomi-cve-2019-18371.yml
    - yonyou-erp-u8-file-upload.yml
    - mingyu-waf-login-bypass.yml
    - amcrest-cve-2017-8229-info-leak.yml
    - anymacro-mail-fileread.yml
    - anymacro-mail-sql-injection.yml
    - aspcms-sqli.yml
    - changjietong-downloadproxy-file-read.yml
    - cisco-router-cve-2019-1653-info-leak.yml
    - cisco-rv-series-router-cve-2021-1472-rce.yml
    - cisco-rv132w-router-cve-2018-0127-information-disclosure.yml
    - cockpit-cve-2020-35846-sqli.yml
    - discuz-cve-2019-13956-rce.yml
    - dlink-cve-2021-42627-unauth.yml
    - e-cology-loginsso-sqli.yml
    - ecology-hrmcareerapplyperview-sql.yml
    - fanweioa-signaturedownload-file-read.yml
    - fumengyun-ajaxmethod-name-sqli.yml
    - h3c-cvm-upload-file-upload.yml
    - h3c-er3100-unauth-access.yml
    - huawei-dg8045-deviceinfo-info-leak.yml
    - inspur-clusterenginev4-sysshell-remote-command-exec.yml
    - kavita-cover-upload-file-read.yml
    - kingdee-k3-cloud-mobfileupload-upload.yml
    - kkfileview-getcorsfile-ssrf.yml
    - kkfileview-xss-cve-2022-35151.yml
    - landray-oa-datajson-rce.yml
    - lg-n1a1-nas-cnnvd-201607-467-rce.yml
    - lucee-cve-2021-21307-rce.yml
    - manageengine-servicedesk-cve-2017-11512-lfi.yml
    - netgear-cnnvd-201306-024.yml
    - nuuo-nvrmini-cve-2018-14933.yml
    - oracle-ebs-bispgrapgh-file-read.yml
    - qiwang-erp-sql-injection.yml
    - reporter-file-read.yml
    - sanfor-reporter-anyfileread.yml
    - solarview-compact-rce-cve-2022-29298.yml
    - telesquare-cve-2021-46422-rce.yml
    - tongda-anywhere2017-auth-bypass.yml
    - tongda-oa-file-read.yml
    - tongda-oa-get-contactlist-php-leak-information.yml
    - tongda-oa-v11-auth-mobi-php-get-user-session.yml
    - topapp-lb-any-user-login.yml
    - totolink-cve-2022-25076-rce.yml
    - u5cms-cve-2022-32444-url-redirection.yml
    - unraid-cve-2020-5847-remote-code-execution.yml
    - vicidial-info-leak.yml
    - wanhu-ezoffice-documentedit-sqli.yml
    - wanhu-ezoffice-downloadservlet-path-traversal.yml
    - wavlink-cve-2022-2486-rce.yml
    - wavlink-cve-2022-31845.yml
    - wavlink-cve-2022-31846.yml
    - wavlink-cve-2022-34046.yml
    - wavlink-cve-2022-34049.yml
    - wavlink-cve-2022-34570-info-leak.yml
    - wavlink-nightled-remote-command-execute.yml
    - wavlink-password-disclosure-cve-2022-34047.yml
    - weaver-oa-filedownload-jsp-path-traversal.yml
    - weaver-oa-jqueryfiletree-jsp-path-traversal.yml
    - weaver-oa-sptmforportalthumbnail-jsp-path-traversal.yml
    - weaver-oa-ultra-vires.yml
    - weblogic-local-cve-2022-21371-file-inclusion.yml
    - wordpress-wp-google-maps-cve-2019-10692-sqli.yml
    - yongyou-chanjet-sql-injection.yml
    - yonyou-fe-templateoftaohong-manager-jsp-directory-traversal.yml
    - yonyou-iufo-userinformation-disclosure.yml
    - zimbra-cve-2022-27925.yml
    - zyxel-cve-2022-0342-auth-bypass.yml
    - zyxel-vmg1312-b10d-cve-2018-19326-path-traversal.yml
    - zyxel-ztp-rce-cve-2022-30525.yml
    - tongda-oa-unauth.yml
    - 74cms-cve-2020-22211-sqli.yml
    - alibaba-nacos-cve-2021-29442-unauth.yml
    - artica-pandora-fms-cve-2020-8497-unauth.yml
    - bitbucket-unauth.yml
    - citrix-cve-2020-8194-code-injection.yml
    - dahua-dss-file-read.yml
    - drawio-cve-2022-1713-ssrf.yml
    - dzzoffice-2-02-1-sc-utf8-unauth.yml
    - dzzoffice-2-02-1-sc-utf8-xss.yml
    - emerge-e3-series-cve-2022-31269.yml
    - eweaver-eoffice-ajax-upload.yml
    - glpi-barcode-cve-2021-43778-path-traversal.yml
    - glpi-telemetry-cve-2021-39211-info-leak.yml
    - gurock-testrail-cve-2021-40875-info-leak.yml
    - hwl-2511-ss-cve-2022-36554-rce.yml
    - intouch-access-anywhere-cve-2022-23854-lfi.yml
    - ispyconnect-cve-2022-29775-unauth.yml
    - kyocera-printer-cve-2020-23575-path-traversal.yml
    - laravel-filemanager-cve-2022-40734-path-traversal.yml
    - msnswitch-cve-2022-32429.yml
    - node-red-cve-2021-25864-fileread.yml
    - oracle-ebs-cve-2018-3167-ssrf.yml
    - panabit-syaddmount-command-exec.yml
    - prtg-network-monitor-cve-2020-11547-info-leak.yml
    - redash-cve-2021-41192-unauth.yml
    - springboot-jolokia-lfi.yml
    - tapestry-cve-2019-0195-readfile.yml
    - teampass-cve-2020-12478-unauth.yml
    - thinfinity-virtualui-cve-2021-44848-user-enum-unauth.yml
    - tongda-oa-gateway-path-traversal.yml
    - wapples-filrewall-cve-2022-35413.yml
    - wavlink-cve-2022-31847-info-leak.yml
    - weaver-e-cology-dbconfigreader-jsp-info-leak.yml
    - weaver-e-cology-ktreeuploadaction-upload.yml
    - weaver-e-mobile-client-do-sqli.yml
    - weaver-e-mobile-ognl-inject.yml
    - wordpress-backupbuddy-cve-2022-31474-lfi.yml
    - yonyou-u8c-file-upload.yml
    - zoho-manageengine-adaudit-plus-cve-2022-28219-xxe.yml
    - 74cms-sqli-cve-2020-22209.yml
    - aruba-instant-default-password.yml
    - cloud-oa-system-sqli.yml
    - dahua-dss-arbitrary-file-download-cnvd-2020-61986.yml
    - dell-idarc-default-password.yml
    - eyou-mail-rce-cnvd-2021-26422.yml
    - f5-big-ip-cve-2022-1388-rce.yml
    - hongfan-ioffice-oa-cnvd-2021-32400-sqli.yml
    - iceflow-vpn-cnvd-2016-10768-info-leak.yml
    - kevinlab-bems-backdoor-cve-2021-37292.yml
    - microsoft-exchange-ssrf-cve-2021-26885.yml
    - pfsense-rce-cve-2021-41282.yml
    - ruijie-eg-update-rce.yml
    - venustech-tianyue-default-password.yml
    - wanhu-ezoffice-file-upload.yml
    - weaver-eoffice-arbitrary-cnvd-2021-49104-file-upload.yml
    - xieda-oa-artibute-cnvd-2021-29066-file-read.yml
    - crawlab-users-add.yml
    - microweber-cve-2022-0666.yml
    - ucms-v148-cve-2020-25483.yml
    - wordpress-photo-gallery-cve-2022-1281.yml
    - eoffice10-file-upload.yml
    - topsec-rce.yml
    - weaver-oa-workrelate-file-upload.yml
    - zentao-sqli-cnvd-2022-42853.yml
    - dataease-cve-2022-34114.yml
    - gogs-cve-2018-18925-rce.yml
    - landray-oa-treexml-rce.yml
    - node-red-file-read.yml
    - specoweb-cve-2021-32572-fileread.yml
    - wi-fi-web-rce.yml
    - yonyou-ksoa-file-upload.yml
    - wanhu-oa-officeserver-file-upload.yml
    - yonyou-grp-u8-file-upload.yml
    - cobub-channel-cve-2018-8057-sqli.yml
    - cuberite-cve-2019-15516.yml
    - greencms-cve-2018-12604.yml
    - junams-fileupload-cnvd-2020-24741.yml
    - rconfig-cve-2020-10546.yml
    - rconfig-cve-2020-10547.yml
    - rconfig-cve-2020-10548.yml
    - rconfig-cve-2020-10549.yml
    - strs-mas-remote-command-exec.yml
    - webgrind-index-cve-2018-12909-fileread.yml
    - youphptube-cve-2019-18662.yml
    - apache-spark-rce-cve-2022-33891.yml
    - confluence-cve-2022-26138.yml
    - h3c-route-unauthorized.yml
    - nps-auth-bypass.yml
    - seeyon-default-password.yml
    - spiderflow-save-remote-command-execute.yml
    - spring-cve-2020-5398-rfd.yml
    - topsec-defalut-password.yml
    - weaver-oa-cnvd-2022-43245.yml
    - yonyou-nc-file-accept-upload.yml
    - yonyou-nc-xxe.yml
    - zhiyuan-oa-fanruan-info-leak.yml
    - zhiyuan-oa-wpsassistservlet-file-upload.yml
    - fanruan-v9-file-upload.yml
    - hexinchuang-cloud-desktop-file-upload.yml
    - hongfan-oa-sqli.yml
    - kingsoft-tss-v8-file-upload.yml
    - lianruan-uninac-fileupload.yml
    - nagiosxi-cve-2020-35578-rce.yml
    - topsec-topapp-lb-sqli.yml
    - trs-was5-file-read.yml
    - weaver-emobile-v6-sqli.yml
    - wordpress-file-manager-cve-2020-25213-file-upload.yml
    - zentao-v11-sqli.yml
    - vbulletin-cve-2015-7808.yml
    - yonyou-chanjet-file-upload.yml
6. 对如下插件规则进行修复，增强：
    - full-read-ssrf-in-spring-cloud-netflix.yml
    - wuzhicms-v410-sqli.yml
    - nexusdb-cve-2020-24571-path-traversal.yml
    - joomla-cve-2015-7297-sqli.yml
    - joomla-cve-2017-8917-sqli.yml
    - zcms-v3-sqli.yml
    - 74cms-sqli.yml
    - discuz-v72-sqli.yml
    - duomicms-sqli.yml
    - seacms-sqli.yml
    - f5-big-ip-cve-2022-1388-rce.yml
    - nginx-cve-2017-7529-info-leak.yml
    - cerebro-request-ssrf.yml
    - openfire-cve-2019-18394-ssrf.yml
    - prometheus-url-redirection-cve-2021-29622.yml
    - zoho-cve-2022-23779-info-leak.yml
    - xunchi-cnvd-2020-23735-file-read.yml
    - 74cms-se-cve-2022-33095.yml
    - e-office-v10-sqli.yml
    - weaver-emobile-v6-sqli.yml
    - mingyu-waf-login-bypass.yml
    - confluence-cve-2021-26084-rce
    - hadoop-yarn-unauthorized-access
    - confluence-cve-2019-3396-path-traversal
    - httpd-ssrf-cve-2021-40438
    - terramaster-cve-2020-28188-rce
    - laravel-cve-2021-3129-rce
    - seeyon-oa-arbitrary-auth
    - qizhi-unauthorized-access
    - yonyou-nc-javabeanshell-rce
    - apache-httpd-cve-2021-41773
    - gitlab-cve-2021-22214-ssrf
    - niushop-attrarray-sqli
    - phpmyadmin-wooyun-2016-199433-deserialization
    - php-cgi-cve-2012-1823-rce
    - apache-druid-cve-2021-36749-file-read
    - elasticsearch-cve-2015-3337



## 1.9.1(2022-07-25)

### 更新内容
1.9.1将会是全版本的更新，支持Window/Mac(darwin)/Linux三大平台，为Mac ARM用户准备了arm64版本。祝师傅们工作愉快~

1.9.1同样为xray2.0的过渡版本，引入了配置的变化，还请师傅们注意配置的更新，备份必要的配置。

```bash
# Example:

# C段扫描
xray x -t example.com/24
# 扫描80，443端口
xray x -t example.com -p 80,443
# 扫描全端口
xray x -t example.com -p -
# 不进行漏洞扫描（或在配置中禁用vuln-scan插件）
xray x -t example.com -skip-web
```

新增端口扫描功能，热门POC135个。详情如下：

* 增加了一些功能
    * 扫描时，可以指定POC的危害等级，分为`low，medium，high，critical`，通过`--level`参数指定
    * 为shiro插件添加文件加载功能，可以直接加载指定文件中的key
    * 可在配置文件中配置每个poc的标签，通过--tags来指定标签扫描
    * 更新了`--list`功能，可查看相关标签对应poc
    * 更新了一些函数与关键词，相关说明将更新在文档中
        * `dir()`
        * `basename()`
        * `body_string`
        * `title_string`
    * 新增主动端口扫描功能（x命令）

        1. 支持对发现的web站点进行漏洞探测
        2. 支持带宽控制与智能速率调节，最优化扫描效率
        3. 支持多目标多端口随机探测，基于有限元的随机化方案
        4. 支持ICMP/TCP/UDP主机存活探测
        5. 支持SYN/CONNECT端口扫描
        6. 支持URL/IP/域名/IP范围/CIDR等多种输入方式
        7. 支持指纹识别
        8. 支持自定义Go语言编写的插件（内测中）

* 修复了一些问题
    * 修复struts插件会使cup占用升高的问题
    * 过滤部分冗余的错误日志

* 新增如下热门漏洞poc，感谢师傅们的提交，更新后即可自动加载。
    * adobe-coldfusion-cve-2021-21087.yml
    * alibaba-anyproxy-fetchbody-fileread.yml
    * apache-apisix-cve-2020-13945-rce.yml
    * apache-guacamole-default-password.yml
    * atlassian-jira-cve-2019-3403.yml
    * bsphp-unauthorized-access.yml
    * cve-2017-16894-sensitive-documents.yml
    * delta-entelitouch-cookie-user-password-disclosure.yml
    * domoticz-cve-2019-10664.yml
    * druid-cve-2021-25646.yml
    * dynamicweb-cve-2022-25369.yml
    * egroupware-spellchecker-rce.yml
    * elfinder-cve-2021-32682-rce.yml
    * emerge-e3-cve-2019-7256.yml
    * essl-dataapp-unauth-db-leak.yml
    * finecms-cve-2018-6893.yml
    * franklinfueling-cve-2021-46417-lfi.yml
    * fuelcms-cve-2018-16763-rce.yml
    * genixcms-register-cve-2015-3933-sqli.yml
    * getsimple-cve-2019-11231.yml
    * ghostscript-cve-2018-19475-rce.yml
    * jetty-servlets-concatservlet-information-disclosure-cve-2021-28169.yml
    * jetty-web-inf-information-disclosure-cve-2021-34429.yml
    * jira-cve-2021-26086.yml
    * joomla-history-cve-2015-7857-sqli.yml
    * jquery-picture-cut-upload-php-fileupload-cve-2018-9208.yml
    * jsrog-artifactory-cve-2019-9733.yml
    * kibana-cve-2019-7609-rce.yml
    * kodexplorer-directory-traversal.yml
    * maccms-cve-2017-17733-rce.yml
    * metabase-cve-2021-41277.yml
    * nostromo-cve-2011-0751-directory-traversal.yml
    * nuxeo-cve-2018-16341-rce.yml
    * odoo-cve-2019-14322.yml
    * php-imap-cve-2018-19518-rce.yml
    * phpmoadmin-cve-2015-2208-rce.yml
    * piwigo-cve-2022-26266-sqli.yml
    * rconfig-ajaxserversettingschk-cve-2019-16662-rce.yml
    * rconfig-commands-inc-cve-2020-10220-sqli.yml
    * resin-directory-traversal-cve-2021-44138.yml
    * ruanhong-jvm-lfi.yml
    * ruanhong-oa-xxe.yml
    * ruckus-default-password.yml
    * seeyon-oa-a8-m-information-disclosure.yml
    * showdoc-cnvd-2020-26585.yml
    * socomec-cve-2019-15859.yml
    * spring-data-rest-cve-2017-8046-rce.yml
    * subrions-search-cve-2017-11444-sqli.yml
    * teclib-glpl-cve-2019-10232.yml
    * terramaster-tos-cve-2022-24989.yml
    * tibco-jasperreports-cve-2018-18809-directory-traversal.yml
    * tongda-oa-login-code-php-login-bypass.yml
    * twonkyserver-cve-2018-7171-fileread.yml
    * vmware-workspace-cve-2021-22054-ssrf.yml
    * vmware-workspace-cve-2022-22954-rce.yml
    * vtigercrm-cve-2020-19363.yml
    * weaver-ecology-getsqldata-sqli-rce.yml
    * wordpress-site-editor-cve-2018-7422-lfi.yml
    * wso2-cve-2022-29464-fileupload.yml
    * wuzhicms-cve-2018-11528.yml
    * zabbix-cve-2019-17382.yml
    * zimbra-collaboration-server-cve-2013-7091-lfi.yml
    * zoneminder-cve-2016-10140-unauth-access.yml
    * apollo-default-password.yml
    * ecology-oa-eoffice-officeserver-php-file-read.yml
    * dptech-vpn-fileread.yml
    * ezoffice-filupload-controller-getshell.yml
    * yachtcontrol-webapplication-cve-2019-17270.yml
    * atlassian-jira-cve-2019-3401.yml
    * emerge-e3-cve-2019-7254.yml
    * vbulletin-cve-2020-12720.yml
    * netsweeper-webadmin-cve-2020-13167.yml
    * searchblox-cve-2020-35580.yml
    * opensis-cve-2020-6637.yml
    * hd-network-real-time-monitoring-system-cve-2021-45043.yml
    * visual-tools-dvr-vx16-cve-2021-42071.yml
    * jsrog-artifactory-cve-2019-17444.yml
    * reolink-RLC-410W-CVE-2022-21236.yml
    * tlr-2005ksh-cve-2021-45428.yml
    * zoho-manageengine-access-manager-plus-cve-2022-29081.yml
    * selea-ocr-anpr-arbitrary-get-file-read.yml
    * easyappointments-cve-2022-0482.yml
    * netgear-ssl-vpn-20211222-cve-2022-29383.yml
    * hitachi-vantara-pentaho-business-analytics-cve-2021-34684.yml
    * manageengine-opmanager-cve-2020-11946.yml
    * intelbras-wireless-cve-2021-3017.yml
    * sapido-router-unauthenticated-rce.yml
    * china-telecom-zte-f460-rce.yml
    * china-mobile-yu-router-information-disclosure.yml
    * tlr-2855ks6-arbitrary-file-creation-cve-2021-46418.yml
    * uniview-isc-rce.yml
    * feiyuxing-route-wifi-password-leak.yml
    * changjie-crm-sqli.yml
    * fhem-file-read-cve-2020-19360.yml
    * hikvision-ip-camera-backdoor.yml
    * kyocera-file-read.yml
    * niushop-cms-sqli.yml
    * dlink-dap-1620-firmware-cve-2021-46381.yml
    * emby-mediaserver-cve-2020-26948.yml
    * zoho-manageengine-opmanager-cve-2020-12116.yml
    * zabbix-cve-2022-23134.yml
    * tieline-ip-audio-gateway-cve-2021-35336.yml
    * selea-ocr-anpr-arbitrary-seleacamera-file-read.yml
    * microweber-cve-2022-0378.yml
    * atlassian-jira-cve-2022-0540.yml
    * sophosfirewall-bypass.yml
    * zoho-manageengine-desktop-central-cve-2021-44515.yml
    * tenda-11n-ultra-vires.yml
    * tenda-w15e-passsword-leak.yml
    * ziguang-sqli-cnvd-2021-41638.yml
    * kemai-ras-ultra-vires.yml
    * cerebro-request-ssrf.yml
    * motioneye-info-leak-cve-2022-25568.yml
    * yinda-get-file-read.yml
    * jupyter-notebook-rce.yml
    * e-message-unauth.yml
    * kkfileview-cve-2021-43734.yml
    * dlink-dsl-28881a-ultra-vires.yml
    * kunshi-vos3000-fileread.yml
    * reolink-nvr-configuration-disclosure-cve-2021-40150.yml
    * d-Link-dir-825-cve-2021-46442.yml
    * vite-cnvd-2022-44615.yml
    * gitblit-cve-2022-31268.yml
    * bigant-server-cve-2022-23347-lfi.yml
    * wordpress-page-builder-kingcomposer-cve-2022-0165-url-redirect.yml
    * huayu-reporter-rce.yml
    * d-link-dap-2020-cve-2021-27250.yml
    * 74cms-se-cve-2022-29720.yml
    * 74cms-se-cve-2022-33095.yml
    * pbootcms-rce-cve-2022-32417.yml
    * e-office-v10-sqli.yml
    * yonyou-nc-file-upload.yml
    * xiaomi-cve-2019-18371.yml
    * yonyou-erp-u8-file-upload.yml
    * mingyu-waf-login-bypass.yml
* 以下POC有所修改
    * poc-yaml-confluence-cve-2021-26084-rce
    * poc-yaml-hadoop-yarn-unauthorized-access
    * poc-yaml-confluence-cve-2019-3396-path-traversal
    * poc-yaml-httpd-ssrf-cve-2021-40438
    * poc-yaml-terramaster-cve-2020-28188-rce
    * poc-yaml-laravel-cve-2021-3129-rce
    * poc-yaml-seeyon-oa-arbitrary-auth
    * poc-yaml-qizhi-unauthorized-access
    * poc-yaml-yonyou-nc-javabeanshell-rce
    * poc-yaml-apache-httpd-cve-2021-41773
    * poc-yaml-gitlab-cve-2021-22214-ssrf
    * poc-yaml-niushop-attrarray-sqli
    * poc-yaml-phpmyadmin-wooyun-2016-199433-deserialization
    * poc-yaml-php-cgi-cve-2012-1823-rce
    * poc-yaml-apache-druid-cve-2021-36749-file-read
    * poc-yaml-elasticsearch-cve-2015-3337

## 1.9.0(2022-06-21)

### 1.9来啦

本次更新的1.9.0为预览版本，可能存在一些不稳定因素，**不建议用于线上生产用途**
目前仅提供linux架构的更新，其他平台的版本会于近期更新。


**欢迎体验新版特性~**

1.9系列将作为2.0的过渡版本，逐步由当前架构切换到全新架构。

在此期间，旧版本的 `config.yml` 和新版本的`plugin-config.xray.yaml`,`module-config.xray.yaml` 将同时存在。

### 本次新增`x`命令
该命令实际上是xray内置的、启用了

- printer
- service-scan
- target-parse

这三个内置的插件的命令。

其中service-scan提供` 主机存活探测`、`服务指纹识别`、`web指纹识别` 的功能

可以查看 `plugin-config.xray.yaml`,`module-config.xray.yaml`获得详细配置信息，执行`xray x --help` 获取命令行参数与试用方法。

示例：
```bash=
xray x -t example.com
xray x -t http://example.com
xray x -t example.com/24
xray x -t 192.168.1.1/24
xray x -t 192.168.1.1-192.168.1.254
xray x -t 192.168.1.1-254
xray x -t 192.168.1.1-254 -p 22,80,443-445
```

### 动态插件加载尝鲜
本次更新新增动态加载golang插件的能力，详细使用手册会与近期更新，敬请期待。

尝鲜体验：

将以下内容保存为example.go，放在./plugin目录下（该目录可在module-config.xray.yaml中配置），即可加该提供 `目标归属地查询` 功能的载示例插件。

放置并重新执行`xray x`后提示：
```bash=
..
config updated:
        get-ip-location-info
```
即加载成功

示例插件：

```go=
package example

import (
	"fmt"
	"github.com/chaitin/xray/x"
	"io/ioutil"
	"net/http"
	"strings"
)

type ExampleConfig struct {
	ServiceURL     string `yaml:"service_url" #:"查询归属地服务的URL"`
	DisableExample bool   `yaml:"disable_example" #:"禁用查询归属地服务" cli:"disable-example,de"`
}

func NewExamplePlugin(e *x.Engine, log x.Logger, reverse *x.Reverse, client *x.Client) {
	config := &ExampleConfig{
		ServiceURL: "http://ip.fm/?ip=%s",
	}

	plugin := e.Register(&x.Plugin{
		Name:   "get-ip-location-info",
		Detail: "获取ip的归属地信息",
		Config: config,
	})

	plugin.Events(func(target x.EventTarget) error {
		if config.DisableExample {
			return nil
		}
		req, err := http.NewRequest(http.MethodGet, fmt.Sprintf(config.ServiceURL, target.IP), nil)
		if err != nil {
			return err
		}
		req.Header.Set("User-Agent", "curl/")

		resp, err := http.DefaultClient.Do(req)
		if err != nil {
			return err
		}
		defer resp.Body.Close()

		info, err := ioutil.ReadAll(resp.Body)
		if err != nil {
			return err
		}
		log.Info(strings.TrimSpace(string(info)))
		return nil
	})
}

```












## 1.8.5(2022-06-13)

### 预告：
本次更新将是1.8版本的最后一次更新，同时会在本周将会再次发布preview版本的xray，该版本将会加入端口指纹识别于动态插件加载功能，欢迎各位师傅下载体验
### 更新内容：
* 增加了一些功能
    * 添加burp的history导出文件转yml脚本的功能
    * log4j2-rce的检测
    * 为自定义脚本(gamma)添加格式化时间戳函数
    * [为自定义脚本(gamma)添加进制转换函数](guide/poc/example/bytes/bformat.md)
    * 为自定义脚本(gamma)添加sha，hmacsha函数
    * 为自定义脚本(gamma)添加url全字符编码函数
* 修复了一些问题
    * 修复`cve-2021-29490`误报严重问题
    * 修复报告只显示参考链接，不显示提交者的问题
    * 修复cache可能出现的请求不发送问题
* 新增如下热门漏洞 poc，感谢师傅们的提交，更新后即可自动加载。
    * vmware-vcenter-cve-2021-21985-rce.yml
    * 74cms-cnvd-2021-45280.yml
    * adobe-coldfusion-cve-2018-15961.yml
    * ametys-cms-cve-2022-26159.yml
    * anmei-rce.yml
    * apache-airflow-cve-2020-13927-unauthorized.yml
    * apache-apisix-dashboard-api-unauth-rce.yml
    * atlassian-jira-unauth-user-enumeration.yml
    * auerswald-cve-2021-40859.yml
    * clickhouse-http-unauth.yml
    * cve-2022-24990-terramaster-fileupload.yml
    * dedecms-cve-2017-17731-sqli.yml
    * dedecms-mysql-error-trace.yml
    * dedecms-search-php-sqli.yml
    * doccms-sqli.yml
    * earcms-download-php-exec.yml
    * earcms-index-uplog-php-file-upload.yml
    * emlog-cve-2021-3293.yml
    * ewebs-fileread.yml
    * eyoucms-cve-2021-39501.yml
    * ezoffice-smartupload-jsp-upload.yml
    * finecms-getshell.yml
    * full-read-ssrf-in-spring-cloud-netflix.yml
    * grafana-snapshot-cve-2021-39226.yml
    * hadoop-yarn-rpc-rce.yml
    * hikvision-readfile.yml
    * hongfan-oa-readfile.yml
    * interlib-read-file.yml
    * ivanti-endpoint-manager-cve-2021-44529-rce.yml
    * jinhe-oa-readfile.yml
    * joomla-jck-cve-2018-17254-sqli.yml
    * kingdee-oa-apusic-readfile.yml
    * landray-oa-rce.yml
    * lionfish-cms-image-upload-php-upload.yml
    * lionfish-cms-wxapp-php-upload.yml
    * mastodon-cve-2022-0432.yml
    * metersphere-plugincontroller-rce.yml
    * metinfo-x-rewrite-url-sqli.yml
    * movabletype-cve-2021-20837-rce.yml
    * netpower-readfile.yml
    * nette-framework-cve-2020-15227-rce.yml
    * nginx-path-traversal.yml
    * oa8000-workflowservice-sqli.yml
    * onethink-sqli.yml
    * php-chat-live-uploadimg-html-upload.yml
    * phpcms-960-sqli.yml
    * phpweb-appplus-php-upload.yml
    * pigcms-file-upload.yml
    * prestashop-smartblog-cve-2021-37538.yml
    * qibocms-readfile.yml
    * rudloff-alltube-cve-2022-0692.yml
    * seeyon-oa-a6-information-disclosure.yml
    * spring-cloud-gateway-cve-2022-22947-rce.yml
    * supesite-sqli.yml
    * sysaid-itil-cve-2021-43972.yml
    * tongda-oa-action-upload-php-upload.yml
    * tongda-oa-report-bi-func-php-sqli.yml
    * voipmonitor-cve-2022-24260.yml
    * wanhuoa-upload-rce.yml
    * weaver-e-office-lazyuploadify-upload.yml
    * weaver-oa-eoffice-information-disclosure.yml
    * weijiaoyi-post-curl-ssrf.yml
    * western-digital-mycloud-ftp-download-exec.yml
    * western-digital-mycloud-jqueryfiletree-exec.yml
    * western-digital-mycloud-multi-uploadify-file-upload.yml
    * western-digital-mycloud-raid-cgi-exec.yml
    * western-digital-mycloud-sendlogtosupport-php-exec.yml
    * western-digital-mycloud-upload-php-exec.yml
    * western-digital-mycloud-upload-php-upload.yml
    * yonyou-erp-nc-readfile.yml
    * zhixiang-oa-sqli.yml
    * zoho-cve-2022-23779-info-leak.yml

## 1.8.4(2022-01-30)

- 新增如下热门漏洞 poc，感谢师傅们的提交，更新后即可自动加载
    + apache-storm-unauthorized-access.yml
    + confluence-cve-2021-26085-arbitrary-file-read.yml
    + dahua-cve-2021-33044-authentication-bypass.yml
    + exchange-cve-2021-41349-xss.yml
    + gocd-cve-2021-43287.yml
    + grafana-default-password.yml
    + hikvision-unauthenticated-rce-cve-2021-36260.yml
    + jellyfin-cve-2021-29490.yml
    + jinher-oa-c6-default-password.yml
    + kingdee-eas-directory-traversal.yml
    + pentaho-cve-2021-31602-authentication-bypass.yml
    + qilin-bastion-host-rce.yml
    + secnet-ac-default-password.yml
    + spon-ip-intercom-file-read.yml
    + spon-ip-intercom-ping-rce.yml
- yaml 脚本部分更新
    - 增加了 http request 和 response 的 raw_header 方法
    - 增加了 bicontains 和 faviconHash 函数
    - 增加了 payloads 结构
    - 增加了 http path 的表达能力，使用 `^` 来访问绝对路径
    - 文档更新 [更新 PR](https://github.com/chaitin/xray_document/pull/8/files)
        - 更新了上面新增的内容
        - 更新了如何处理转义字符的说明，并提出了 multipart 中`\r\n` 的解决方法
        - 更新了 http path 如何使用的文档
        - 更新了 payload 如何使用的文档
        - 更新了 webhook 的部分内容

## 1.8.2 (2021-11-07)
- 新增如下热门漏洞 poc，感谢师傅们的提交，更新后即可自动加载
    + apache-ambari-default-password.yml
    + apache-druid-cve-2021-36749.yml
    + apache-httpd-cve-2021-40438-ssrf.yml
    + apache-httpd-cve-2021-41773-path-traversal.yml
    + apache-httpd-cve-2021-41773-rce.yml
    + apache-nifi-api-unauthorized-access.yml
    + e-zkeco-cnvd-2020-57264-read-file.yml
    + ecology-arbitrary-file-upload.yml
    + ecshop-collection-list-sqli.yml
    + h5s-video-platform-cnvd-2020-67113-unauth.yml
    + hikvision-intercom-service-default-password.yml
    + jetty-cve-2021-28164.yml
    + laravel-cve-2021-3129.yml
    + novnc-url-redirection-cve-2021-3654.yml
    + ruijie-eg-cli-rce.yml
    + ruijie-eg-file-read.yml
    + tvt-nvms-1000-file-read-cve-2019-20085.yml
    + zzcms-zsmanage-sqli.yml
- 子域名爆破配置文件修改，调高了子域名爆破的默认并发(`subdomain.max_parallel` 30 -> 500)，添加了新增 API 的配置，新增 API 包括（可以重新生成配置，也可以在 `subdomain.sources` 直接添加）：
```yaml
    alienvault:
      enabled: true
    bufferover:
      enabled: true
    fofa:
      enabled: false
      email: ""
      key: ""
    ip138:
      enabled: true
    myssl:
      enabled: true
    riskiq:
      enabled: false
      user: ""
      key: ""
    quake:
      enabled: false
      key: ""
```
- 修复 lint 会跳过文件的问题
- 修复了子域名爆破日志太多的问题，降低了部分日志的 `log level`
- 修复了 `--poc` 指定内置 poc 时，任何 poc 都不会加载的问题
- 修复了转换旧格式脚本可能的 panic 问题


## 1.8.1 (2021-10-25)

+ 推出了自定义脚本的 V2 版本
    * 修改了 `lintpoc` 命令
    * 添加了 `transform v1` 命令用于 v1 格式的转换
    * 文档更新 https://docs.xray.cool/#/guide/poc/v2
+ 子域名爆破添加了一些插件，可在 `config.yml` 查看具体内容
+ 升级 go 版本到 1.17， 支持了 mac M1 (`xray_darwin_arm64`）
+ 修复了一些问题
    * 修复了自动更新的问题
    * 修复了子域名爆破缓慢的问题
    * 优化了重名 poc 加载问题，只打印 log，不退出，以系统内置的为准
+ 新增如下热门漏洞 poc，感谢师傅们的提交，更新后即可自动加载。
    * alibaba-canal-default-password.yml
    * amtt-hiboss-server-ping-rce.yml
    * confluence-cve-2021-26084.yml
    * datang-ac-default-password-cnvd-2021-04128.yml
    * dlink-cve-2020-25078-account-disclosure.yml
    * dubbo-admin-default-password.yml
    * ecology-v8-sqli.yml
    * eea-info-leak-cnvd-2021-10543.yml
    * elasticsearch-cve-2015-5531.yml
    * f5-cve-2021-22986.yml
    * gateone-cve-2020-35736.yml
    * gitlab-graphql-info-leak-cve-2020-26413.yml
    * gitlab-ssrf-cve-2021-22214.yml
    * gitlist-rce-cve-2018-1000533.yml
    * h3c-imc-rce.yml
    * h3c-secparh-any-user-login.yml
    * hanming-video-conferencing-file-read.yml
    * hikvision-info-leak.yml
    * hjtcloud-arbitrary-fileread.yml
    * hjtcloud-directory-file-leak.yml
    * huawei-home-gateway-hg659-fileread.yml
    * iis-put-getshell.yml
    * inspur-tscev4-cve-2020-21224-rce.yml
    * jeewms-showordownbyurl-fileread.yml
    * jellyfin-file-read-cve-2021-21402.yml
    * kingsoft-v8-default-password.yml
    * kingsoft-v8-file-read.yml
    * kubernetes-unauth.yml
    * kyan-network-monitoring-account-password-leakage.yml
    * landray-oa-custom-jsp-fileread.yml
    * metinfo-file-read.yml
    * mpsec-isg1000-file-read.yml
    * natshell-arbitrary-file-read.yml
    * netentsec-icg-default-password.yml
    * netentsec-ngfw-rce.yml
    * node-red-dashboard-file-read-cve-2021-3223.yml
    * ns-asg-file-read.yml
    * panabit-gateway-default-password.yml
    * panabit-ixcache-default-password.yml
    * pbootcms-database-file-download.yml
    * prometheus-url-redirection-cve-2021-29622.yml
    * qizhi-fortressaircraft-unauthorized.yml
    * rabbitmq-default-password.yml
    * ruijie-eweb-rce-cnvd-2021-09650.yml
    * ruijie-nbr1300g-cli-password-leak.yml
    * ruijie-uac-cnvd-2021-14536.yml
    * ruoyi-management-fileread.yml
    * saltstack-cve-2021-25282-file-write.yml
    * samsung-wlan-ap-wea453e-rce.yml
    * sangfor-ba-rce.yml
    * seeyon-a6-employee-info-leak.yml
    * seeyon-a6-test-jsp-sql.yml
    * seeyon-oa-cookie-leak.yml
    * seeyon-session-leak.yml
    * shiziyu-cms-apicontroller-sqli.yml
    * shopxo-cnvd-2021-15822.yml
    * showdoc-default-password.yml
    * showdoc-uploadfile.yml
    * skywalking-cve-2020-9483-sqli.yml
    * solr-fileread.yml
    * tamronos-iptv-rce.yml
    * telecom-gateway-default-password.yml
    * tianqing-info-leak.yml
    * tongda-user-session-disclosure.yml
    * tpshop-directory-traversal.yml
    * vmware-vrealize-cve-2021-21975-ssrf.yml
    * weiphp-path-traversal.yml
    * weiphp-sql.yml
    * wifisky-default-password-cnvd-2021-39012.yml
    * xdcms-sql.yml
    * yapi-rce.yml
    * yongyou-u8-oa-sqli.yml
    * yonyou-nc-bsh-servlet-bshservlet-rce.yml
    * zabbix-default-password.yml



## 1.7.1 (2021-3-18)

+ 优化自定义POC开发体验。
    * 优化poc加载逻辑，通过 `webscan -poc` 指定poc时无需再添加 `--plugin phantasm` 参数。
    * 新增自动加载本地自定义POC配置 `auto_load_poc` ，启用后，除内置poc外，会自动加载当前目录以 "poc-" 为文件名前缀的POC文件。
    * 增加指令别名。如 `ws` 等同于 `webscan` 、 `-u` 等同于 `-url` 、`-p` 等同于 `-poc`。详细改动可通过 `--help` 查看。
        * 例如，可使用如下命令，仅运行当前目录下的 poc 且 不运行内置 poc 进行测试：`ws -p ./poc-* -u http://example.com`
+ 首次启动自动生成配置文件，优化新用户使用体验。
+ 修复部分插件误报的问题，优化扫描效果。
+ 新增如下热门漏洞 poc，感谢师傅们的提交，更新后即可自动加载。
    * `poc-yaml-activemq-default-password`
    * `poc-yaml-airflow-unauth`
    * `poc-yaml-alibaba-canal-info-leak`
    * `poc-yaml-apache-kylin-unauth-cve-2020-13937`
    * `poc-yaml-ecshop-cnvd-2020-58823-sqli`
    * `poc-yaml-ecshop-rce`
    * `poc-yaml-exchange-cve-2021-26855-ssrf`
    * `poc-yaml-odoo-file-read`
    * `poc-yaml-rockmongo-default-password`
    * `poc-yaml-sonicwall-ssl-vpn-rce`
    * `poc-yaml-vmware-vcenter-unauthorized-rce-cve-2021-21972`
    * `poc-yaml-yonyou-grp-u8-sqli`

## 1.7.0 (2021-1-20)

+ webscan 增加 `--burp-file xxx` 的输入来源，可以用于解析 burp 的导出文件并扫描
+ 为 yaml poc 增加 `response.latency` 表示响应延迟，单位毫秒
+ 修复 yaml poc 对 bytes 替换的问题，可以编写 body 是 bytes 的 poc 了
+ 新增如下热门漏洞 poc，感谢师傅们的提交，更新后即可自动加载
    * `poc-yaml-alibaba-nacos-v1-auth-bypass`
    * `poc-yaml-chinaunicom-modem-default-password`
    * `poc-yaml-citrix-xenmobile-cve-2020-8209`
    * `poc-yaml-craftcms-seomatic-cve-2020-9757-rce`
    * `poc-yaml-dlink-dsl-2888a-rce`
    * `poc-yaml-dotnetcms-sqli`
    * `poc-yaml-flink-jobmanager-cve-2020-17519-lfi`
    * `poc-yaml-frp-dashboard-unauth`
    * `poc-yaml-go-pprof-leak`
    * `poc-yaml-jira-cve-2019-8442`
    * `poc-yaml-jumpserver-unauth-rce`
    * `poc-yaml-kafka-manager-unauth`
    * `poc-yaml-lanproxy-cve-2021-3019-lfi`
    * `poc-yaml-nps-default-password`
    * `poc-yaml-opentsdb-cve-2020-35476-rce`
    * `poc-yaml-ruijie-eg-rce`
    * `poc-yaml-samsung-wea453e-default-pwd`
    * `poc-yaml-samsung-wea453e-rce`
    * `poc-yaml-seeyon-ajax-unauthorized-access`
    * `poc-yaml-seeyon-cnvd-2020-62422-readfile`
    * `poc-yaml-solarwinds-cve-2020-10148`
    * `poc-yaml-sonarqube-cve-2020-27986-unauth`
    * `poc-yaml-springboot-env-unauth`
    * `poc-yaml-terramaster-cve-2020-15568`
    * `poc-yaml-terramaster-cve-2020-28188-rce`
    * `poc-yaml-vmware-vcenter-arbitrary-file-read`
    * `poc-yaml-yonyou-nc-arbitrary-file-upload`
    * `poc-yaml-zeit-nodejs-cve-2020-5284-directory-traversal`
    * `poc-yaml-zeroshell-cve-2019-12725-rce`


## 1.6.1 (2020-12.27)

+ 修复上一版本 poc 逻辑问题导致的大量误报的问题

## 1.6.0 (2020-12.25)
大家圣诞节快乐！让 xray 陪你度过 2020 的最后几天吧。

Features:
+ poc 增加规则组的概念 `groups`，可以在一个 yaml 写多组规则表示同一漏洞的不同情况，详见文档
+ poc 增加 `sleep` 函数，表示暂停执行当前上下文多少秒
+ 升级 go 编译器版本至 1.15.6
+ windows 下使用新的图标构建应用
+ 感谢师傅们提交的如下 POC，xray 更新后即可自动加载，无需改动配置文件
    + `poc-yaml-jira-cve-2020-14179` @harris2015
    + `poc-yaml-minio-default-password` @harris2015
    + `poc-yaml-kibana-cve-2018-17246` @canc3s
    + `poc-yaml-seeyon-wooyun-2015-148227` @canc3s
    + `poc-yaml-laravel-improper-webdir` @Dem0ns
    + `poc-yaml-powercreator-arbitrary-file-upload` @MrP01ntSu
    + `poc-yaml-h2-database-web-console-unauthorized-access` @jujumanman

Fixes:
+ 修复 dirscan 因 partial 导致的误报问题
+ 修复 dirscan svn 规则容易误报的问题
+ 修复 dirscan 自定义字典报错提前退出的问题
+ 修复 struts 过滤规则过严导致漏洞扫不出的问题
+ 修复多个 poc rule 报告中只显示最后一个请求响应的问题
+ 修复单 url 扫描可能提前退出的问题

## 1.5.0 (2020-11-24)
Features:
+ shiro 检测增加对 gcm 加密方式的支持，现在会使用 gcm 和 cbc 模式分别进行探测
+ cors 支持检测允许任意源携带认证信息 `baseline/cors/any-origin-with-credential`

Fixes:
+ 修复统计错误导致的疑似卡住问题
+ 修复 `detect_xss_in_cookie` 配置失效的问题 https://github.com/chaitin/xray/issues/942

## 1.4.5 (2020-11-18)

Features:
+ 增加 convert 命令，用于 json 和 html 结果的互相转换，漏洞结果和子域名均支持转换
    + `xray convert --from 1.json --to 1.html`
    + `xray convert --from 1.html --to 1.json`
+ 同步社区新增 POC，无需更新配置文件即可使用
    + `poc-yaml-saltstack-cve-2020-16846`
    + `poc-yaml-dlink-cve-2020-9376-dump-credentials`
+ 增加国内下载站，支持通过 `./xray upgrade` 一键快速更新

Fixes:
+ 修复因调度问题导致的 shiro 扫不出利用链的问题 https://github.com/chaitin/xray/issues/928
+ 修复基础爬虫/浏览器爬虫设置代理无效的问题 https://github.com/chaitin/xray/issues/926
+ 修复因请求跳转导致的 shiro 漏报问题
+ 修复部分漏洞输出的请求响应不正确的问题
+ 修复 phantasm 加载 poc 的一系列问题
+ 进一步修复系统路径泄露的误报问题
+ 修复远程反连的情况下健康检查失败的问题

Changes:
+ 对非 html 类型的响应在输出漏洞时做了 64K 的大小限制，防止生成的报告过大
+ 为避免磁盘占用过大，windows 下去重不再使用 badger，而是直接使用 syncmap
+ webhook 不再使用配置文件中的代理

## 1.4.2 (2020-11-10)

该版本修复了上一版本中比较影响使用的问题，包括

+ 修复配置文件部分项不起作用的问题
+ 修复系统路径泄露误报比较多的问题
+ 修复 windows 下 badger 数据多次访问的问题
+ 修复 mitm 数据缓存残留问题
+ 文件输出异常时的 panic 的问题

## 1.4.1 (2020-11-6)

+ 修复上个版本 mitm 命令行参数失效的问题

## 1.4.0 (2020-11-6)

Hi，好久不见。在这看似宁静的一个月里我们并没有停下脚步，而是由内而外的对 xray 做了亿点点调整。 这是一个充满 Break Change 的版本，不可避免的会出现一些新的 Bug，希望大家能及时反馈以便我们修复。

Features:
- 更换为基于事件驱动的插件调度器，同时重新定义内部插件结构，细化插件粒度；去除了调度中大量消息同步逻辑。总之就是更快、更强
- 采用全新的配置文件结构，同时改进配置加载方式，后续无需删除原配置文件即可使用各种新特性、新 POC
- 全局更换基于 badger 的新型过滤器，只要存储足够大，轻松过滤一个亿
- 为 phantasm 增加两项新配置，均支持 glob 语法进行批量匹配
    - `local_poc` 加载本地的 poc, 如：`/home/poc/*`
    - `exclude_poc`  排除哪些 poc，如 `/home/poc/*web*`、`poc-yaml-weblogic*`
- 增加多种针对资源限制的配置定义，可以按需使用，具体支持的例子参照配置文件说明
    - `hostname_allowed` 允许访问的 hostname，不带端口
    - `port_allowed` 允许访问的端口
    - `path_allowed` 允许访问的 url path
    - `query_key_allowed` 允许访问的 url query key
    - `post_key_allowed` 允许访问的请求 body 的 key
    - `fragment_allowed` 允许访问的 fragment
- 增加 baseline 插件的检测项
    - `detect_system_path_leak` 检查响应是否包含系统路径泄露
    - `detect_private_ip`       检查响应是否包含内网 ip
- 同步社区新增 POC
    - `poc-yaml-jira-cve-2020-14181` jira 用户枚举漏洞
    - `poc-yaml-weblogic-cve-2020-14750` Weblogic console 权限绕过漏洞
    - `poc-yaml-seacmsv645-command-exec` seacms 某rce
    - `poc-yaml-seacms-before-v992-rce` seacms 某rce
- 增加在注入点在 cookie 中的 sqli 检测，默认开启且可配置
- `webscan` 增加 `--list` 参数，用于展示所有内置的可用插件
- 优化部分界面的交互体验，增加更多提示信息

BugFixes:
- 修复 dirscan 逻辑问题导致的错误文件名拼接
- 改进页面相似度比较算法，可以修复部分 sql 注入和 dirscan 的误报问题
- 尝试修复 `poc-yaml-phpstudy-nginx-wrong-resolve` 误报较多的问题

Changes:
- 调整漏洞输出结构，改动将影响 json/html/webhook 输出，文档稍后补充
- 反连平台不再默认启动，需要用户手动配置后使用，否则部分漏洞扫不出
- 融合社区高级版和社区普通版的配置文件，高级版特有的命令普通用户也可见，但非高级版用户无法使用
- 子域名的 HTML 报告名改为后缀添加，比如 `a.com.html`、`a.com-500.html`


## 1.3.2 (2020-09-17)

Featues:
- 同步社区新增检测 POC，支持当下热门漏洞检测，**需自行合并到配置文件或重新生成配置文件来使用 ,仅更新版本不更新配置文件不会生效！**

    - `poc-yaml-yonyou-grp-u8-sqli-to-rce`  用友 GRP-U8 注入&远程命令执行
    - `poc-yaml-sangfor-edr-cssp-rce` 深信服 EDR CSSP 3.2.21 任意代码执行
    - `poc-yaml-nsfocus-uts-password-leak` 绿盟 UTS 综合威胁探针管理员任意登录
    - `poc-yaml-weaver-ebridge-file-read-linux` 泛微 Ebridge 任意文件读取 - linux
    - `poc-yaml-weaver-ebridge-file-read-windows` 泛微 Ebridge 任意文件读取 - windows
    - `poc-yaml-thinkadmin-v6-readfile` ThinkAdmin V6 任意文件读取
    - `poc-yaml-phpstudy-nginx-wrong-resolve` phpstudy nginx 解析漏洞
    - `poc-yaml-hikvision-cve-2017-7921` 海康威视用户泄露&未授权访问
    - `poc-yaml-thinkcmf-lfi` Thinkcmf 框架文件包含
    - `poc-yaml-ueditor-cnvd-2017-20077-file-upload` UEditor 任意文件上传
    - `poc-yaml-xiuno-bbs-cvnd-2019-01348-reinstallation` 修罗BBS重安装漏洞
    - `poc-yaml-xunchi-cnvd-2020-23735-file-read` 迅优cms任意文件读取


## 1.3.0 （2020-08-27）

Features:
- 改进高级版授权验证机制，旧版授权不再支持，**需要联系管理员重新签发**
- 改进普通爬虫实现逻辑，更加可靠和稳定
- 部分平台自动调整 rlimit 限制，避免因此导致的请求失败
- baseline 插件新增检查项，需自行开启:
    - `detect_china_bank_card` 检查银行卡号泄露
    - `detect_china_address` 检测街道地址泄露
- 同步社区新增检测 POC，支持部分 HW 热门漏洞检测，需自行合并到配置文件或重新生成配置文件
    - `poc-yaml-sangfor-edr-arbitrary-admin-login`
    - `poc-yaml-sangfor-edr-rce`
    - `poc-yaml-tongda-meeting-unauthorized-access`
    - `poc-yaml-citrix-cve-2020-8193-unauthorized`
    - `poc-yaml-bt742-pma-unauthorized-access`
    - `poc-yaml-apache-ofbiz-cve-2020-9496-xml-deserialization`
    - `poc-yaml-apacheofbiz-cve-2018-8033-xxe`
    - `poc-yaml-jenkins-unauthorized-access`
    - `poc-yaml-joomla-component-vreview-sql`
    - `poc-yaml-joomla-cve-2018-7314-sql`
    - `poc-yaml-jupyter-notebook-unauthorized-access`
    - `poc-yaml-nexusdb-cve-2020-24571-path-traversal`
    - `poc-yaml-openfire-cve-2019-18394-ssrf`
    - `poc-yaml-wordpress-cve-2019-19985-infoleak`

Bugfixes:
- 修复 sql 注入部分网站扫不出的问题 https://github.com/chaitin/xray/issues/834
- 修复 shiro 自定义 aes_key 不生效的问题
- 修复 `poc-yaml-thinkcmf-write-shell` 为闭合导致的错误
- 修复 `poc-yaml-draytek-cve-2020-8515` 的误报


## 1.2.0 （2020-08-12）

Features:
- 高级版新增浏览器爬虫支持
    - 独立二进制，需单独下载安装使用 https://github.com/chaitin/rad
    - 高级版支持一键扫描`webscan --browser-crawler http:/xxx`
- 同步社区版 POC 改动
    - 新增 `poc-yaml-vbulletin-cve-2019-16759-bypass`
    - 修复 `poc-yaml-citrix-cve-2020-8191-xss`

Bugfixes:
- shiro 扫描增加重确认机制，避免误报
- 修复 `.svn/entries` 扫不出的问题 https://github.com/chaitin/xray/issues/820

## 1.1.8 (2020-08-04)

Bugfixes:
- 修复 Shiro 检测遇到多个 `deleteMe` 时漏报的问题
- 修复 Tomcat 回显 `ClassNotFoundException` 的问题
- 修复 `poc-yaml-draytek-cve-2020-8515` 误报问题
- 修复 `poc-yaml-jira-ssrf-cve-2019-8451` 误报问题
- 修复 `.so` 结尾的网址不被扫描的问题
- 修复 baseline 插件部分漏洞重复问题
- 修复部分场景下 cookie 配置不生效的问题

Changes:
- 子域名dns server 为空时不再报错退出，而是使用默认 server

## 1.1.6 (2020-07-30)

Features:
- 进一步改进 shiro 检测插件
    - 使用新的方法检测 shiro key，完全去除反连平台的依赖，思路来自 @l1nk3r
    - 支持配置默认使用的 cookie name，默认为 `rememberMe`
    - 支持自动监测并扫描非默认 cookie name 的情况
- 增加 `force-ssl` 配置项，可以与 `raw-request` 搭配使用来扫描单个 ssl 请求
- 新增如下社区提交 POC，已经打包到二进制中，完整使用需删除配置文件并重新生成
    - poc-yaml-harbor-cve-2019-16097
    - poc-yaml-fortigate-cve-2018-13379-readfile
    - poc-yaml-draytek-cve-2020-8515
    - poc-yaml-cisco-cve-2020-3452-readfile

Bugfixes:
- 修复 `poc-yaml-jira-cve-2019-8449` 误报问题

## 1.1.0（2020-07-16）
Features:
- 新增 shiro 漏洞检测插件 (仅高级版)，支持 RememberMe 反序列化漏洞检测
    - 内置独家 Java 反序列化利用链，有效作用于 shiro 环境
    - 内置 Payload 支持全版本 Tomcat 回显，Tomcat 6,7,8,9 均通过测试
    - 借助反连平台可以完美支持非 Tomcat 环境的漏洞检测
    - 完整转置 ysoserial 至 Go 代码，无需 Java 依赖即可运行
    - 自带 shiro 100 key，且支持通过配置文件自定义 shiro key
- 基于端口复用技术，合并 RMI 与 HTTP 服务，简化部署成本
- baseline 插件新增手机号泄露检测，需手动开启 `detect_china_phone_number`
- 子域名 html 报告按 500 条分报告写入，防止数据太大打不开 https://github.com/chaitin/xray/issues/792
- 新增下列社区贡献 POC，已经打包到二进制中，目前共计 170 POC，完整使用需删除配置文件并重新生成
    - poc-yaml-apache-flink-upload-rce
    - poc-yaml-aspcms-backend-leak
    - poc-yaml-citrix-cve-2020-8191-xss
    - poc-yaml-consul-rexec-rce
    - poc-yaml-consul-service-rce
    - poc-yaml-f5-tmui-cve-2020-5902-rce
    - poc-yaml-nexus-default-password
    - poc-yaml-qnap-cve-2019-7192
    - poc-yaml-spring-cloud-cve-2020-5410

Bugfixes:
- 修复 baseline 插件 header 值大小写误报问题 https://github.com/chaitin/xray/issues/796
- POC 语法中，header 的 key 改为大小写不敏感
- 修复两个 POC 的误报问题
    - `poc-go-tongda-arbitrary-auth`
    - `joomla-cnvd-2019-34135-rce.yml`

Changes:
- 配置文件、证书文件、授权文件都从 xray 二进制所在目录加载而不是 shell 当前目录

## 1.0.4 (2020-06-23)

Features:
- 反连平台支持配置 ip 获取方式`reverse/http/ip_header`，以支持在 Nginx 等反向代理后工作的场景。

Bugfixes:
- 修复 header 配置不生效的问题
- 修复基础爬虫中一处竞争问题
- 修复部分情况下高级版授权加载失败的问题
- 修复 `laravel-debug-info-leak` poc 的误报

Changes:
- 由于发现 `4.2.2.1` 存在大量误解析的情况，子域名配置中的 servers 包含`4.2.2.1` 时将默认去除该 server

## 1.0.0 (2020-06-13)

我们在去年的今天[发布了 xray 0.1.0 版本](https://github.com/chaitin/xray/releases/tag/0.1.0)，一年的时间内，我们发布了 43 个版本，公开收集了 157 个 poc，已经成为安全社区最好用的工具之一。

为了感谢大家对 xray 的支持，一周年特别准备了“阳光普照奖": 扫码关注 xray 社区公众号，回复 "高级版领取"，即可得到两个月有效期的 xray 高级版。

注意：下载后需要将文件名手动改为 `xray-license.lic`，放置在 xray 二进制同一目录并删除之前的配置文件才能使用高级版的全部功能。

----

以下为 xray 一周年纪念版的更新内容

Features:
- 重新打造企业级子域名收集模块，在高级版中提供体验
    - 智能 dns server 调度算法，保证扫描可靠性和稳定性，同时保持低资源占用
    - 集成爆破、api、html 页面、js 分析、域传送等多种收集模块
    - 新增子域名 webhook、json 和 html 报告文件输出
- http 客户端支持配置多个代理、根据域名切换代理、根据权重进行代理负载均衡等，使用方法见 https://xray.cool/xray/#/configration/http
- 命令注入模块增加非注入型代码执行检测
- fastjson 模块增加部分 waf 绕过 payload
- 优化 jsonp、thinkphp 等模块的实现
- dirscan 支持根据 url 动态生成 payload，如访问 `s.php` 将会尝试扫描 `.s.php.swp` 等敏感文件
- dirscan 支持 sourcemap 泄露扫描
- Windows 二进制文件增加图标

Bugfixes:
- 修复 sql 注入、bruteforce 等模块中的一些问题
- 修复 fastjson 报告输出中，没有 request body 的问题
- 修复 `tomcat-cve-2017-12615-rce`、`tongda-lfi-upload-rce` 等 poc 的误报
- 修复 json 参数解析和替换的一些问题
- 修复 html 报告中 url 过长导致页面溢出的问题

Changes:
- 移除交互式 shell 功能
- 使用全新的配置文件格式，旧版本的配置将不再兼容，需要备份删除，xray 将自动生成新版配置文件
    - 修改了 http 配置，支持配置多个代理
    - 修改了 http 配置，将 header 与 cookie 配置项合并和简化
    - 隐藏了 baseline 中部分意义不大的选项
