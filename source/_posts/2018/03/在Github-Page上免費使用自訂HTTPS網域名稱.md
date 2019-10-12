---
title: 在Github-Page上免費使用自訂HTTPS網域名稱
tags:
  - Github
  - SSL
abbrlink: 2e36326e
date: 2018-03-04 16:33:13
keywords:
  - Github
  - HTTPS
  - 網域
  - 免費
---
# Why 

在[Github](https://github.com/)上建好靜態網站後, 都是利用 username.github.io 進行瀏覽
但是[Github](https://github.com/)也有可以讓我們使用自己的域名(Domain Name)的設定
假設選擇自訂域名就無法使用它所提供的SSL連線服務, 需要找第三方來使用HTTPS

![Github自訂域名](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1520154032/blog/2018/03/04/在Github-Page上免費使用HTTPS自定網域名稱/github_domain.png)

為了達成HTTPS自訂域名, 需要要利用[CloudFlare](https://www.cloudflare.com/)所提供的免費服務
至於堅持使用HTTPS的原因, 我到時候再寫另外一篇文章詳細補充

# How

1. 確認已經註冊好一個域名

2. 確認已經在[Github](https://github.com/)上建立一個靜態網站

3. 前往[CloudFlare](https://www.cloudflare.com/)註冊然後按照指示加入你的站點

4. 到Github靜態網站的Repo裡面點選設定, 然後往下拉找到自訂網域(Custom domain), 填上要設定的網域按下儲存

5. 回到[CloudFlare](https://www.cloudflare.com/)在DNS頁面新增CNAME連結到 username.github.io (username為Github用戶名)
在這個範例設定中, 我要讓原本的主機名稱連到原先架設在GCP上的網站, 而blog子網域連到Github的靜態網頁

![CNAME](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/c_scale,h_250/v1520160636/blog/2018/03/04/在Github-Page上免費使用HTTPS自定網域名稱/dns.png)
(第二個CNAME的紀錄是為了讓不論前面有沒有輸入www的網址都能連線到我的網站)
確認成功連線應該會出現如下的Active字樣(設定完成需要等5~10分鐘生效)

![Status: Active](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1520161993/blog/2018/03/04/在Github-Page上免費使用HTTPS自定網域名稱/active.png)

6. 如果原本有設定過DNS的部分, 要去把他們都替換成CloudFlare提供的DNS Server(在DNS設定頁籤中)

![CloudFlare DNS Server](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1520161622/blog/2018/03/04/在Github-Page上免費使用HTTPS自定網域名稱/dnscf.png)

7. 接著設定SSL的部分, 選擇加密(Crypto)頁籤SSL是否為FULL模式(若沒有手動設定成FULL)(設定完成需要等5~10分鐘)
當畫面顯示Active Certificate字樣代表SSL已啟用

![Active Certificate](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1520162125/blog/2018/03/04/在Github-Page上免費使用HTTPS自定網域名稱/sslactive.png)

8. 此時應該已經可以使用https訪問我們的Github page了, 但若是使用者輸入http則無效, 所以需要作強制導向HTTPS的設定
選擇頁面規則(Page Rules)後點選新增規則
在URL Match的部分打上http的網域名, 最後加上星號代表此網域底下通用規則, 然後設定選擇Always Use HTTPS強制導向

![強制導向HTTPS](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1520162472/blog/2018/03/04/在Github-Page上免費使用HTTPS自定網域名稱/pagerulehttps.png)

9. 這時候就算URL輸入http也會自動連向https的頁面了, 設定完成!

## 備註

+ 如果使用的是[Jekyll](https://jekyllrb.com/), [Hexo](https://hexo.io/)之類的靜態網站產生器(Static Site Generator)的話, 記得內容連結也都要是https,否則會出現Mix Content的問題, 這樣是無法看到SSL綠色鎖頭的哦

+ 使用[Hexo](https://hexo.io/)這種利用Push更新文章的靜態網站, 因為每次更新都會把全部檔案洗掉, 所以我們要把設定Github Custom Domain所產生的CNAME檔案(無附檔名), 複製一份放在Hexo的source資料夾, 才不會造成CNAME被覆蓋掉而要重新設定的情況
CNAME內容就是Github Page的Custom Domain, 也可以自己建立一份, 例如
```ini
blog.driftkingtw.me
```

+ 可以在[CloudFlare](https://www.cloudflare.com/)的Page Rules頁籤新增Cache規則, 加快網站載入速度

## 參考資料
[Secure and fast GitHub Pages with CloudFlare](https://blog.cloudflare.com/secure-and-fast-github-pages-with-cloudflare/)
[Setting up a custom subdomain](https://help.github.com/articles/setting-up-a-custom-subdomain/)
