---
title: Laravel Linux 佈署問題筆記
tags:
  - PHP
  - Laravel
  - Linux
keywords:
  - PHP
  - Laravel
  - Linux
  - Server
abbrlink: 91e1ab9c
date: 2018-07-10 00:49:57
---

整理了自己在佈署Laravel專案到Server時(Linux)所遇到的一些問題處理方式。
環境若沒特別說明就代表使用的OS為Ubuntu

### Q1. 如何設定Laravel .env檔的APP_KEY?

<strong>Ans：</strong>在專案跟目錄執行 `php artisan key:generate`

### Q2. 如何從Github上Clone私人專案到網站目錄

<strong>Ans：</strong> git clone https://username@github.com/username/repo
其中`username` 為Github帳號名稱，`repo` 為專案名稱。

### Q3. 在瀏覽器開啟網頁時出現以下錯誤訊息
`Error in exception handler: The stream or file "/var/www/laravel/app/storage/logs/laravel.log" could not be opened: failed to open stream: Permission denie`

<strong>Ans：</strong> 目錄權限不足，在專案目錄執行 `sudo chmod -R 777 storage` (注意，此方法較不安全)

安全的做法是把不同的權限分開，使用ACL(Access Control List)設定

```shell
// nginx = web server user
// systemuser = your local user which you use to login via ssh
sudo setfacl -Rdm u:nginx:rwx,u:systemuser:rwx storage
sudo setfacl -Rm u:nginx:rwx,u:systemuser:rwx storage
```

如果沒有ACL的設定方法

```shell
sudo chgrp -R www-data storage bootstrap/cache
sudo chmod -R ug+rwx storage bootstrap/cache
```

### Q4. 如何切換不同的PHP版本

<strong>Ans：</strong> 安裝好需要的版本後執行以下指令 (範例為 7.0 => 7.2)

停用先前的PHP版本(重要)
```shell
sudo a2dismod php7.0
```
啟動所需的PHP版本
```shell
sudo a2enmod php7.2
```
重啟Apache2服務
```shell
sudo service apache2 restart
```

### Q5. 首頁能夠正常顯示，但切換不同的 Get 路徑顯示 404 Not Found (Ex: www.domainname.com/login)

<strong>Ans：</strong>

啟動Rewrite模組

```shell
sudo a2enmod rewrite
```

重啟Apache2服務

```shell
sudo service apache2 restart
```

重啟後還是顯示404錯誤，使用文字編輯器開啟 `000-default.conf` 設定檔 (個人習慣使用vim)

```shell
sudo vim /etc/apache2/sites-available/000-default.conf
```

在 `DocumentRoot /var/www/html` 下方新增以下設定

```apacheconf
<Directory /var/www/html>
  AllowOverride All
</Directory>
```

儲存設定後，重啟Apache2服務，這時就能正常顯示 GET 路徑了

```shell
sudo service apache2 restart
```

## 參考資料

[Cloning a private Github repo](https://stackoverflow.com/questions/2505096/cloning-a-private-github-repo)

[Laravel framework file permission - Security](https://laracasts.com/discuss/channels/general-discussion/laravel-framework-file-permission-security)

[Laravel 5.4 problem - Wrong Route (Not Found) on Ubuntu 16.04](https://www.youtube.com/watch?v=7CatEn5IAlo)

---

日後遇到其他問題會再持續更新