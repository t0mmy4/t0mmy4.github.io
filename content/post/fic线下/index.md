---
title: "fic线下"
description: ""
slug: "fic线下"
date: "2025-05-24 12:40:23+08:00"
image: "assets/Pasted image 20250524132344.png"
categories: 
    - forensic
tags: 
    - fic
draft: false
---

# 计算机
## 1. 请分析检材2，该检材系统中设备名称为neo4chen的系统分区的sha256值为
|                                                                  |
| ---------------------------------------------------------------- |
| E2219548F5A8E61F373258EB658625ADDA7BF858A83AD8D6E4213BCD8ECEB423 |
## 2. 请分析检材2，上题系统中，曾被远程控制的ip为
![](<assets/Pasted image 20250524132344.png>)
## 3. 请分析检材2加密系统，陈某通过物理方式记录的助记词上方文字为
# 服务器
## 1. 请分析检材3，该操作系统版本号为
![](<assets/Pasted image 20250524133500.png>)
## 2. 请分析检材3，该主机名为
![](<assets/Pasted image 20250524133536.png>)
## 3. 请分析检材3，该ens33网卡IP地址为
![](<assets/Pasted image 20250524164623.png>)
## 4. 请分析检材3，操作系统登录使用了第三方身份验证，该技术为
关键在看root的历史命令
![](<assets/Pasted image 20250524165008.png>)
## 5. 请分析检材3，该身份验证的加密算法为？
Bcrypt
## 6. 请分析检材3，该保存king用户密码的文件名为？
![](<assets/Pasted image 20250524165056.png>)
my_two_factor_pwdfile
## 7. 请分析检材3，尝试爆破king用户，其密码为(king字母加3个数字)？
king:$2a$10$V/8GUI5aTNSnbFodPjP7Zu6vCFSXmvdd9oKHGtWv/vbT3Q4LLTCcW
```python
with open('passwords.txt', 'w') as file:
    for i in range(10000):
        password = f"king{i:03d}"
        file.write(password + '\n')
print("密码本生成完成！已创建 passwords.txt 文件")
``` 
生成密码本，爆破，hash格式为bcrypt
```shell
PS D:\pwntool\hashcat-6.2.6> .\hashcat.exe -m 3200 hash.txt .\passwords.txt
hashcat (v6.2.6) starting

* Device #1: WARNING! Kernel exec timeout is not disabled.
             This may cause "CL_OUT_OF_RESOURCES" or related errors.
             To disable the timeout, see: https://hashcat.net/q/timeoutpatch
* Device #2: WARNING! Kernel exec timeout is not disabled.
             This may cause "CL_OUT_OF_RESOURCES" or related errors.
             To disable the timeout, see: https://hashcat.net/q/timeoutpatch
nvmlDeviceGetFanSpeed(): Not Supported

CUDA API (CUDA 12.8)
====================
* Device #1: NVIDIA GeForce RTX 4060 Laptop GPU, 7099/8187 MB, 24MCU

OpenCL API (OpenCL 3.0 CUDA 12.8.51) - Platform #1 [NVIDIA Corporation]
=======================================================================
* Device #2: NVIDIA GeForce RTX 4060 Laptop GPU, skipped

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 72

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Single-Hash
* Single-Salt

Watchdog: Temperature abort trigger set to 90c

Host memory required for this attack: 158 MB

Dictionary cache built:
* Filename..: .\passwords.txt
* Passwords.: 10000
* Bytes.....: 99000
* Keyspace..: 10000
* Runtime...: 0 secs

$2a$10$V/8GUI5aTNSnbFodPjP7Zu6vCFSXmvdd9oKHGtWv/vbT3Q4LLTCcW:king110

Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 3200 (bcrypt $2*$, Blowfish (Unix))
Hash.Target......: $2a$10$V/8GUI5aTNSnbFodPjP7Zu6vCFSXmvdd9oKHGtWv/vbT...LLTCcW
Time.Started.....: Sat May 24 17:06:13 2025 (1 sec)
Time.Estimated...: Sat May 24 17:06:14 2025 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (.\passwords.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:     1280 H/s (6.91ms) @ Accel:1 Loops:16 Thr:24 Vec:1
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 576/10000 (5.76%)
Rejected.........: 0/576 (0.00%)
Restore.Point....: 0/10000 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:1008-1024
Candidate.Engine.: Device Generator
Candidates.#1....: king000 -> king575
Hardware.Mon.#1..: Temp: 49c Util: 99% Core:2520MHz Mem:7001MHz Bus:8

Started: Sat May 24 17:06:04 2025
Stopped: Sat May 24 17:06:15 2025
```
## 8. 请分析检材3，该WEB-API配置的 MySQL 数据库服务器地址为
![](<assets/Pasted image 20250524142636.png>)
## 9. 请分析检材3，其中用于 WEB-API 测试的流量包文件名为
![](<assets/Pasted image 20250524142801.png>)
test.pcap
## 10. 请分析检材3流量包，统计其中 admin 用户成功登录的次数为
![](<assets/Pasted image 20250524143136.png>)
## 11. 请分析检材3流量包，找出用户最后一次查看的商品型号为
![](<assets/Pasted image 20250524144243.png>)
## 12. 请分析检材3WEB-API，该容器镜像ID为(前六位)
![](<assets/Pasted image 20250524144523.png>)
## 13. 请分析检材3WEB-API，该容器的核心服务编程语言为
![](<assets/Pasted image 20250524144442.png>)
## 14. 请分析检材3WEB-API，该容器所用域名为
![](<assets/Pasted image 20250524144640.png>)
## 15. 请分析检材3WEB-API，该容器日志文件名(access_log)为
![](<assets/Pasted image 20250524151716.png>)
## 16. 请分析检材3WEB-API，该容器的FLAG2的接口URL为
![](<assets/Pasted image 20250524144110.png>)
/api/auth/flag2
## 17. 请分析检材3WEB-API，该容器的服务运行状态的接口URL为
![](<assets/Pasted image 20250524151813.png>)
## 18. 请分析检材3WEB-API，该容器中访问FLAG2接口后，会提示需要在什么调试器下运行
GDB
## 19. 请分析检材3WEB-API，该容器的admin用户的登录密码为
zhaohong666
## 20. 请分析检材3WEB-API，该容器的数据库内容被SO所加密，该SO文件名为
![](<assets/Pasted image 20250524151858.png>)
## 21. 请继续分析上题SO文件，该文件的编译器类型为
![](<assets/Pasted image 20250524152105.png>)
## 22. 请继续分析上题SO文件，在SO文件的encrypt函数中，该加密算法为
![](<assets/Pasted image 20250524152219.png>)
## 23. 请继续分析上题SO文件，尝试分析getAeskey函数，该KEY值为
232A2B2C2B252C3E232A2B2C2B252C3E
## 24. 请继续分析上题SO文件，尝试分析get_flag1函数，该返回值为
flag1{hong112233}
