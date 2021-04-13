---
title: 利用 Valine-Admin 與 Gmail 為 Valine 留言系統提供 Email 通知功能及留言管理
tags:
  - hexo
  - valine
  - blog
keywords:
  - hexo
  - valine
  - blog
  - email
  - 通知
  - LeanCloud
abbrlink: 82ae9419
date: 2021-04-13 19:00:00
---

本站使用的是 Valine 留言系統，但是之前忘了加上留言通知導致有些留言沒看到（非常抱歉😅，後來事物繁忙到現在才加上通知功能。

這次就來記錄如何使用 Valine-Admin 透過 Gamil 的 SMPT 服務免費加上 Email 通知功能以及留言管理後台吧！

<!--more-->

# 事前準備

這邊預設已經安裝設定好環境（或已啟用）[Valine](https://valine.js.org/index.html) 留言功能並能夠正常使用，雲端運算平台為 [LeanCloud](https://leancloud.app)（國際版），其實我想用 Firebase 不過 Valine 官方尚未支援😭

# Valine-Admin Email 通知設定

這邊要使用的是官方推薦的 [DesertsP/Valine-Admin](https://github.com/DesertsP/Valine-Admin) 實現即時通知功能，並透過 Gmail 的 SMTP 服務發送 Email。

關於 Gmail 的部分我們要先去 [Google Account/App Password](https://myaccount.google.com/apppasswords) 中進行 App 設定（需要開啟 2FA 驗證，關於 2FA 相關知識可以參考[之前的文章](https://blog.driftking.tw/2020/02/Why-My-Account-Got-Hacked-The-Password-Security-and-Physical-Key/)）

新增一個 App Password，選擇自定選項並輸入一個名稱讓自己知道這串密碼是給什麼服務用的，如下圖：

![App Password](https://res.cloudinary.com/driftkingtw/image/upload/g_auto/v1618308412/blog/2021/04/add-email-notification-to-valine/Screen_Shot_2021-04-13_at_6.04.48_PM.png)

接著 Google 會產生 App 密碼給你，複製黃框區域的密碼，稍後要填到 SMTP 密碼欄位中：

![App Password](https://res.cloudinary.com/driftkingtw/image/upload/g_auto/v1618308411/blog/2021/04/add-email-notification-to-valine/Screen_Shot_2021-04-13_at_6.05.23_PM.png)

再來進入使用 Valine 的 LeanCloud App 中，選擇 `LeanEngine > Web > Settings` 點擊 `Add a new variable` 設定以下各項環境變數（範例僅供參考）：

| 變數            | 範例                      | 說明 |
| -------------- | ------------------------- | ------------   |
| SITE_NAME      | DriftKingTW's Blog        | [必須]網站名稱   |
| SITE_URL       | https://blog.driftking/tw | [必須]網站URL   |
| SMTP_SERVICE   | Gmail                     | [必須]SMTP服務  |
| SMTP_USER      | <Your_Gmail_Account>      | [必須]Gmail帳號 |
| SMTP_PASS      | <Your_Google_App_Pass>    | [必須]剛才產生的App密碼 |
| SENDER_NAME	   | DriftKingTW               | [必須]寄件人名稱 |
| SENDER_EMAIL   | xxx@gmail.com             | [必須]寄件人Email|
| ADMIN_URL      | Text                      | [建議]自動喚醒服務用 |
| BLOGGER_EMAIL  | xxx@gmail.com             | [可選]自己的收件Email|
| AKISMET_KEY	   | XXX                       | [可選]Akismet垃圾留言檢測|

![設定範例](https://res.cloudinary.com/driftkingtw/image/upload/g_auto/v1618304057/blog/2021/04/add-email-notification-to-valine/Screen_Shot_2021-04-13_at_4.50.54_PM.png)

完成後點擊 `Save` 儲存設定

{% colorquote info %}
圖中 `MAIL_SUBJECT`、`MAIL_SUBJECT_ADMIN`、`MAIL_TEMPLATE`、`MAIL_TEMPLATE_ADMIN`為 Email 的樣版設定，會在[自定義 Email 模板](#自定義-Email-模板)設定中提到。
{% endcolorquote %}

# 雲端後台管理部署

首先在環境變數下方找到 `LeanEngine domain` 欄位，填入自己想要的網頁名稱（將來會透過這個網址連接留言管理後台）

![LeanEngine domain](https://res.cloudinary.com/driftkingtw/image/upload/g_auto/v1618304811/blog/2021/04/add-email-notification-to-valine/Screen_Shot_2021-04-13_at_5.05.21_PM.png)

接下來在左方導覽列切換至 `Deploy` 中，選擇 `Deploy from Git` 填入 Git Repo： `https://github.com/DesertsP/Valine-Admin.git`。選擇 `Production` 模式後點擊 `Deploy` 進行手動部署。

![部署設定](https://res.cloudinary.com/driftkingtw/image/upload/g_auto/v1618304654/blog/2021/04/add-email-notification-to-valine/Screen_Shot_2021-04-13_at_5.03.44_PM.png)

完成設定後透過剛才設定的網址（如：[driftkingtw.avosapps.us](https://driftkingtw.avosapps.us/sign-up)，註冊自己的管理員帳密）

{% colorquote info %}
若進入後自動轉跳到登入頁面，需要手動去 `DataStorage` 將 `_User` 資料表清空。
{% endcolorquote %}

完成後即可透過後台網址登入到後台管理系統了！後台的操作應該相當直覺，這邊就不多介紹了😉

# 防止免費版 LeanCloud 自動休眠

由於免費版的 LeanCloud 容器會有自動休眠的限制，一天強制休眠 6hr，半小時內沒有新的請求也會自動進入休眠狀態，雖然發送請求的時候會自動喚醒但該則請求的 Email 通知會發送失敗，以下提供 Valine-Admin 官方的解決方法：

- 自動喚醒

透過環境變數 `ADMIN_URL` 到 `LeanEngine > Scheduled Tasks` 中新建自動喚醒任務：

![自動喚醒](https://res.cloudinary.com/driftkingtw/image/upload/g_auto/v1618309232/blog/2021/04/add-email-notification-to-valine/Screen_Shot_2021-04-13_at_6.20.22_PM.png)

填入自訂名稱後，動作選擇 `self_awake`，Corn 表達式部分填入 `0 */25 0-15,23 * * ?` （國際版 LeanCloud 使用時間為 UTC+0，代表從台北時間 7:00AM～11:50PM 中每30分鐘會自動喚醒）

- 自動重發 24h 內漏發的 Email

原理同上，再新增一個自動任務，動作選擇 `resend_mails` ，Corn 表達式則為 `0 10 23 * * ?` （國際版 LeanCloud 使用時間為 UTC+0，代表從台北時間 7:10AM 會自動重發 24h 內漏發的 Email）

到此基本設定全部完成，可以自己測試一下功能是否正常運作 😎

# 自定義 Email 模板

可以透過新增以下幾種環境變數來達成自定義 Email 模板：

| 變數                  | 說明                 |
| -------------------- | -------------------- |
| MAIL_SUBJECT         | [可選]郵件主旨         |
| MAIL_TEMPLATE        | [可選]郵件樣板         |
| MAIL_SUBJECT_ADMIN   | [可選]通知自己郵件的主旨 |
| MAIL_TEMPLATE_ADMIN  | [可選]通知自己郵件的樣本 |

這邊提供自己翻譯官方樣板的繁體中文及英文版本：

```html
<!-- 繁體中文 MAIL_SUBJECT -->
${PARENT_NICK}，您在『${SITE_NAME}』上的留言有新回應！
```

```html
<!-- 繁體中文 MAIL_TEMPLATE -->
<div
    style="border-top:2px solid #12ADDB;box-shadow:0 1px 3px #AAAAAA;line-height:180%;padding:0 15px 12px;margin:50px auto;font-size:12px;">
    <h2 style="border-bottom:1px solid #DDD;font-size:14px;font-weight:normal;padding:13px 0 10px 8px;"> 您在 <a
            style="text-decoration:none;color: #12ADDB;" href="${SITE_URL}" target="_blank"> ${SITE_NAME}</a> 上的留言有新回應
    </h2> ${PARENT_NICK} 您曾發表留言：<div style="padding:0 12px 0 12px;margin-top:18px">
        <div style="background-color: #f5f5f5;padding: 10px 15px;margin:18px 0;word-wrap:break-word;"> ${PARENT_COMMENT}
        </div>
        <p><strong>${NICK}</strong> 回應：</p>
        <div style="background-color: #f5f5f5;padding: 10px 15px;margin:18px 0;word-wrap:break-word;"> ${COMMENT}</div>
        <p>您可以點擊<a style="text-decoration:none; color:#12addb" href="${POST_URL}" target="_blank">查看完整回應</a>，歡迎再次光臨 <a
                style="text-decoration:none; color:#12addb" href="${SITE_URL}" target="_blank">${SITE_NAME}</a>。<br></p>
    </div>
</div>
```

```html
<!-- 繁體中文 MAIL_SUBJECT_ADMIN -->
${SITE_NAME} 上有新留言！
```

```html
<!-- 繁體中文 MAIL_TEMPLATE_ADMIN -->
<div
    style="border-top:2px solid #12ADDB;box-shadow:0 1px 3px #AAAAAA;line-height:180%;padding:0 15px 12px;margin:50px auto;font-size:12px;">
    <h2 style="border-bottom:1px solid #DDD;font-size:14px;font-weight:normal;padding:13px 0 10px 8px;"> 您在 <a
            style="text-decoration:none;color: #12ADDB;" href="${SITE_URL}" target="_blank">${SITE_NAME}</a> 上的文章有了新的留言！
    </h2>
    <p><strong>${NICK}</strong> 回覆：</p>
    <div style="background-color: #f5f5f5;padding: 10px 15px;margin:18px 0;word-wrap:break-word;"> ${COMMENT}</div>
    <p>您可以點擊<a style="text-decoration:none; color:#12addb" href="${POST_URL}" target="_blank">查看完整留言</a><br></p>
</div>
```

---

```html
<!-- English MAIL_SUBJECT -->
${PARENT_NICK}, your comment on 『${SITE_NAME}』 got a new response!
```

```html
<!-- English MAIL_TEMPLATE -->
<div
    style="border-top:2px solid #12ADDB;box-shadow:0 1px 3px #AAAAAA;line-height:180%;padding:0 15px 12px;margin:50px auto;font-size:12px;">
    <h2 style="border-bottom:1px solid #DDD;font-size:14px;font-weight:normal;padding:13px 0 10px 8px;">Your comment on
        <a style="text-decoration:none;color: #12ADDB;" href="${SITE_URL}" target="_blank"> ${SITE_NAME}</a> have a new
        response!
    </h2> ${PARENT_NICK} You says：<div style="padding:0 12px 0 12px;margin-top:18px">
        <div style="background-color: #f5f5f5;padding: 10px 15px;margin:18px 0;word-wrap:break-word;"> ${PARENT_COMMENT}
        </div>
        <p><strong>${NICK}</strong> Replied：</p>
        <div style="background-color: #f5f5f5;padding: 10px 15px;margin:18px 0;word-wrap:break-word;"> ${COMMENT}</div>
        <p>You can <a style="text-decoration:none; color:#12addb" href="${POST_URL}" target="_blank">view full
                response</a>, Cheers! <a style="text-decoration:none; color:#12addb" href="${SITE_URL}"
                target="_blank">${SITE_NAME}</a>。<br></p>
    </div>
</div>
```

```html
<!-- English MAIL_SUBJECT_ADMIN -->
New comment on ${SITE_NAME}.
```

```html
<!-- English MAIL_TEMPLATE_ADMIN -->
<div
    style="border-top:2px solid #12ADDB;box-shadow:0 1px 3px #AAAAAA;line-height:180%;padding:0 15px 12px;margin:50px auto;font-size:12px;">
    <h2 style="border-bottom:1px solid #DDD;font-size:14px;font-weight:normal;padding:13px 0 10px 8px;">You have a new
        comment on <a style="text-decoration:none;color: #12ADDB;" href="${SITE_URL}" target="_blank">${SITE_NAME}</a> .
    </h2>
    <p><strong>${NICK}</strong> comment：</p>
    <div style="background-color: #f5f5f5;padding: 10px 15px;margin:18px 0;word-wrap:break-word;"> ${COMMENT}</div>
    <p>You can <a style="text-decoration:none; color:#12addb" href="${POST_URL}" target="_blank">view full
            response</a>.<br></p>
</div>
```

記得更新環境變數後要重啟容器才會生效喔，可以試著改成自己想要的樣子 😎

![Email 範例](https://res.cloudinary.com/driftkingtw/image/upload/g_auto/v1618310383/blog/2021/04/add-email-notification-to-valine/Screen_Shot_2021-04-13_at_6.39.24_PM.png)

剩餘的其他詳細功能請參考 [DesertsP/Valine-Admin](https://github.com/DesertsP/Valine-Admin)

# 參考

[DesertsP/Valine-Admin](https://github.com/DesertsP/Valine-Admin)

[issue:关于时区的一个问题](https://github.com/DesertsP/Valine-Admin/issues/63)