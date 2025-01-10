# Wireshark

---
## Wireshark 介紹

:::info
### Wireshark 用途

Wireshark是一個協助使用者收取網路流量並進行分析的工具，
可以利用Wireshark來觀察流經自己電腦上特定網卡的封包，並從中觀察是不是有異常的行為出現，像是有些惡意程式會在電腦背景執行，並持續發送出不明的封包，可能是偷偷向外連線以得到指令，或是偷傳資料出去。
Wireshark在捕捉封包時是記錄所有的封包資訊，不會阻擋封包，因此Wireshark是呈現網路最真實的流量。而他不會主動標註哪些封包可能是具有危險性的，我們必須自己去觀察出哪些是有問題的封包。


:::

:::info
### Wireshark 功能

Wireshark最主要的功能就是讓我們去看到每一個封包的所有詳細訊息。

而他本身也含有許多好用的圖形化工具及分析工具，透過這些工具讓我們能夠更快速檢查網路環境。可以讓我們去找其中是否存在異常流量、也能幫助使用者管理網路環境。

我們也可以把一段時間的網路封包存成一個封包檔，副檔名會是.pcap或.pcapng，以利證據保存或是後續的分析使用。
:::
## Practice

TTL(Time To Live )是IP協定包中的一個值，它告訴網路,數據包在網路中的時間是否太長而應被丟棄。TTL的最初構想是確定一個時間範圍，超過此時間就把包丟棄。由於每個路由器都至少要把TTL域減一，TTL通常表示包在被丟棄前最多能經過的路由器個數。當記數到0時，路由器決定丟棄該包，並傳送一個ICMP報文給最初的傳送者。


:::info

**Secure Shell，SSH**
一個可以用加密連線來與伺服器互動的方法，就是安全外殼協定。

SSH利用==迪菲-赫爾曼密鑰交換（Diffie–Hellman key exchange， D-H）的機制==，
雙方可以先在非加密的連線中雙方建立一個密鑰，接著雙方就可以利用這個密鑰來進行接下來的連線，而且必須符合不用透過非加密連線告知對方這個密鑰是什麼。

![](https://i.imgur.com/OcenhG0.png)

[[Day 6] 這樣算是加密連線嗎？是的，都是SSH](https://ithelp.ithome.com.tw/articles/10193879)
:::

:::info
***HTTP，Hyper Text Transfer Protocol***
是全球資訊網路通信的基礎，這個應用層的通訊協定，最早被設計用來「傳送」及「接收」HTML的頁面及內容。HTTP在用戶端(使用者)與伺服器端(網站)之間透過==TCP協定==來傳遞「請求」與「應答」的要求。

當使用者透過瀏覽器(Safari、Chrome、Edge等)發起一個開啟網頁的請求時，該請求會被送至伺服器網站端，而網站通過預設的==連接埠80==接收，並將網頁HTML的內容(包含文字與圖片)應答傳送回使用者端，這就是資訊透過HTTP傳遞的過程。
:::

:::info
**HTTPS，Hypertext Transfer Protocol Secure**
另一個加密連線協定超文本傳輸安全協定

HTTPS身為加密連線，會以==安全套接層==（==Secure Sockets Layer， SSL==）及==傳輸層安全性協定==（==Transport Layer Security， TLS==）等加密方式來進行加密，TLS其實就是SSL的升級版，SSL目前也已經不再更新，且最高的版本是3.0，TLS目前仍在持續發展中，最高的版本則是1.2。也因為TLS是SSL下一代的版本，所以會有人把TLS 1.0稱做SSL 3.1，TLS 1.1稱做SSL 3.2，並把TLS 1.2稱做SSL 3.3。SSL 3.0前已經是很過時的加密方式，且也曾被發現嚴重的漏洞，所以==建議現在網站的加密方式都要是TLS起跳的才安全==。

網站通過的==連接埠443==接收

***TLS加密方式的流程***


---

1.我的電腦傳送ClientHello的訊息給Facebook，跟他打招呼代表我們要開始跟Facebook建立加密連線了，並提供我的電腦可以接受的加密通訊協定版本、一個用戶端生成的亂數、支援的加密演算法及支援的壓縮演算法。

2.Facebook回覆ServerHello，代表他收到了我的電腦傳過去的招呼，並回傳確認使用的加密通訊協定版本（若是伺服器能提供的最高版本比客戶端來得低的話，就無法建立連線）、一個伺服器生成的亂數、確認使用的加密方法及伺服器憑證。伺服器告知用戶端，接下來將會依照雙方協議的加密方法等連線參數來進行加密，並傳送Finished訊息。

3.用戶端告知伺服器，接下來將會依照雙方協議的加密方法等連線參數來進行加密，並傳送Finished訊息。

![](https://i.imgur.com/o1H2jWn.png)


---

不是所有使用HTTPS的網站都是安全的，Chrome版PTT程式，他雖然表面上是使用HTTPS進行加密連線，但因為裡面的程式實際使用的卻是TELNET的協定，還是有可能造成未加密的資料外洩。
[[Day 7] 摸摸HTTPS](https://ithelp.ithome.com.tw/articles/10194126)
:::

:::info
***與Email相關的幾個常見協定名稱***
1. 簡單郵件傳輸協定（Simple Mail Transfer Protocol, SMTP）
2. 郵局協定第3版（Post Office Protocol，POP）
3. 網際網路資訊存取協定（Internet Message Access Protocol，IMAP ）。
:::

:::info
***要對外網連線，皆會先連線到預設閘道***，IP Address依然是目的地的address，但mac Address是傳送方的Default gateway Mac address
:::

