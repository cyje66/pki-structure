# PKI的基礎架構
## 資訊安全四大基本要求
1. 機密性(Confidentiality)：
* 確保資料傳遞與儲存的秘密性，以避免未經授權的使用者有意或無意的揭露資料內容
* 可透過**加密**方式達到機密性，如：安全傳輸協定 SSL(Secure Socket Layer)、對稱式密碼系統(DES、AES)、非對稱式密碼系統(RSA、ElGamal、ECC) 
2. 完整性(Integrety)：
* 資料文件經傳送或儲存過程中，其內容並未遭到非法竄改或偽造稱之
* 可透過**雜湊函數(hash function)** 達到完整性，如：MD5(128 bits)、SHA-1(160 bits)、SHA-256(256 bits) 
3. 鑑別性(Authentication)：
* 又可分為身份鑑別及資料來源鑑別，前者要能快速且正確地驗證使用者身份，後者要能確認資料訊息之傳輸來源，以避免惡意傳送者假冒某傳送者之身份傳送不安全的訊息內容
* 身份鑑別可透過如：證件驗證、生物特徵驗證、通行密碼驗證達到鑑別性
* 資料來源鑑別可透過如：**數位簽章(digital signature)**或資料加密方式達到鑑別性
4. 不可否認性(Non-repudiation)：
* 確保網路交易的雙方無法否認曾進行過的交易、或通訊參與的雙方皆無法否認曾進行資料傳輸或接收訊息
* 可透過**數位簽章(digital signature)**、公開金鑰基礎架構(PKI; Public Key Infrastructure)達到不可否認性
### 雜湊與加密的不同?
* 加密需要密鑰，且可以透過解密得到原文。（加密可逆）
* 雜湊不需密鑰，無法逆向解出原始輸入。（雜湊不可逆）
* 舉例: 今天如果我拿到一串密文，我是有辦法透過解密，得到原始的明文的，而且如果使用非對稱式加密的話，我甚至還可以驗證傳送方是不是本人（是否有被進行中間人攻擊）。
  * 雜湊演算法在這裡也會被使用，通常會被拿來檢驗傳送的訊息是否有被更改過。
  * 傳送方附上原始訊息經過某個雜湊演算法得出的雜湊值，接收方在解開這個訊息之後，會透過相同的雜湊演算法來驗證。
  * 但如果是拿到一串雜湊值的話，理論上是無法逆向得到原始的明文的，根本無法拿來做資料的傳遞。
## PKI名詞介紹
* 註冊管理中心 RA （Registration Authority）: 通常使用者要向 CA 取得一張憑證，並非直接取得，而是透過一個平台稱為 RA（Registration authority）來進行申請，如同到郵局申辦業務時，是透過櫃台人員進行流程作業，然後櫃台人員再使用背後的資源完成作業需求，而 **RA 的角色就如同櫃台一樣。** 所以一個使用者想要申請一張憑證，也需要透過 RA，來向 CA 申請一張憑證，RA 就像是一個註冊中心，管理所有前來註冊的申請人，**RA 可以進一步事先審核申請人的資料及合法性**，確認申請人的身分是否正確，然後 RA 再向 CA 提出憑證的申請。
* 憑證管理中心 CA（Certificate authority）: 會為每個使用公開金鑰的用戶，來發放一個「數位憑證」，目的是證明這個憑證中列出的用戶，可以合法擁有憑證中列出的公開金鑰，透過憑證來告訴他人我合法擁有了我的公鑰。而這個憑證除了透過公開外，**也包含了CA機構的數位簽章**，一來是公開這份憑證的效力，並使得攻擊者不能偽造和篡改憑證。數位憑證通常包含有持有者的公開金鑰、持有者的電子郵件地址、憑證發行單位、憑證有效期限…等等。
* 目錄服務系統 （Directory service）: 簡單的說，就是你可以提供一個查詢的資料對應的內容，舉例來說，你可以提供一個『帳號名稱』，而在這個帳號名稱底下給予對應了密碼、UID、GID、真實姓名等資訊， 若用戶端使用帳號名稱來查詢目錄服務，則可以得到該帳號的密碼、UID等等資訊就是了。
## SSL/TLS 協定
在一般的情況下，我們都是以http協定來閱讀web上的資料，但http協定中傳送的資料都是以明文的方式傳遞的，所以會有中間人攻擊(man-in-the-middle)的疑慮。為了解決這個問題，我們改採用https(secure http)協定，把中間傳遞的資訊加密，但https要怎麼實現?答案就是透過SSL/TLS這兩個協定。(https = http + ssl)[參考這個影片](https://www.youtube.com/watch?v=hExRDVZHhig)
* SSL(secure sockets layer): 用公開金鑰加密法來加密資料
* TLS(transport layer security): 是更新、更安全的 SSL 版本，可以認證server端跟client端。
## 公開金鑰演算法(非對稱式)
### DSS(Digital Signature Standard) 數位簽章
是美國 NIST（National Institute Standard and Technique）於1994 年所制定的數位簽章標準，此標準制定了『數位簽章演算法』（Digital Signature Algorithm, DSA），並於 2000 年修改成 FIPS PUB 186-2，除了可以選用 RSA 演算法，還增加『橢圓曲線數位簽章演算法』（Elliptic Curve Digital Signature Algorithm, ECDSA）。
#### DSA(Digital Signature Algorithm) 數位簽章演算法
僅供**數位簽章**使用，無法應用於其他地方，而RSA 演算法可以用於加密和簽名，雖然 DSA 演算法不能使用於資料加密或交換鑰匙方面，但亦屬公開鑰匙演算法之一。
[RSA跟DSA的差別](https://www.itread01.com/articles/1501720695.html)
#### RSA
#### ECC(橢圓曲線密碼演算法)

## X.509
X.509是密碼學裡公開金鑰認證的格式標準，X.509憑證里含有公鑰、身分資訊（比如網路主機名，組織的名稱或個體名稱等）和簽章資訊（可以是憑證簽發機構CA的簽章，也可以是自簽章）。
## Stateless vs Stateful
* Stateless: 就是指server不會去記住你現在的動作，例如連上google.com。
* Stateful: server會記住你的動作，例如: 登入gmail.com，沒有登入就不能看信箱裡的內容。
[參考連結](https://www.dotblogs.com.tw/jimmyyu/2010/10/16/difference-between-stateful-and-stateless)
