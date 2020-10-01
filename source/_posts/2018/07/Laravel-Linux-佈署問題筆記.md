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
環境若沒特別說明就代表使用的OS為Ubuntu<!--more-->

### Q1. 如何設定Laravel .env檔的APP_KEY?

<strong>Ans：</strong>在專案跟目錄執行 `php artisan key:generate`

### Q2. 如何從Github上Clone私人專案到網站目錄

<strong>Ans：</strong> 

```shell
git clone https://username@github.com/username/repo
```
其中`username` 為Github帳號名稱，`repo` 為專案名稱。

### Q3. 在瀏覽器開啟網頁時出現以下錯誤訊息
`Error in exception handler: The stream or file "/var/www/laravel/app/storage/logs/laravel.log" could not be opened: failed to open stream: Permission denie`

<strong>Ans：( Apache )</strong> 

目錄權限不足，在專案目錄執行 `sudo chmod -R 777 storage` (注意，此方法較不安全)

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

<strong>Ans：( Nginx )</strong>

```shell
sudo chown USERNAME:USERNAME /var/www/html/SITE_FOLDER
```

### Q4. 如何切換不同的PHP版本 ( Apache )

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

( Nginx )

設定預設PHP版本
```shell
update-alternatives --set php /usr/bin/php7.0
```

### Q5. 首頁能夠正常顯示，但切換不同的 Get 路徑顯示 404 Not Found (Ex: www.domainname.com/login) 

<strong>Ans：( Apache )</strong>

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

<strong>Ans：( Nginx )</strong>

以文字編輯器下列設定檔 `/etc/nginx/sites-enabled/default` 新增下列設定

```nginx
server {
    ...

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    ...
}
```

重整Nginx服務

```shell
sudo systemctl reload nginx
```

### Q6. Bitnami LAMP Stack 更換DocumentRoot目錄？

<strong>Ans：</strong>

更改以下檔案的DocumentRoot，80與443(有SSL的話)。

`YOUR_INSTALLATION_DIRECTORY/apache2/conf/bitnami/bitnami.conf`

重啓Apache

```shell
sudo /opt/bitnami/ctlscript.sh restart apache
```

### Q7. 上傳檔案移動到只訂資料夾時出現權限不足

單純使用chmod無效(由於改變的權限爲user非操作移動的www-data)，需要使用以下方法給予權限。

```shell
sudo chown -R www-data:www-data /path/to/folder
```

### Q8. 利用 Laravel Installer 新增 Laravel 專案 [MacOS]

MacOS 環境變數設定：

```shell
export PATH=~/.composer/vendor/bin:$PATH
```
### Q9. Laravel 7 使用 React 的方法

Laravel 之前使用 React 的指令為：

```php artisan preset react```

7 之後由於移除 ```preset``` 指令，要使用以下方法：

```shell
composer require laravel/ui
php artisan ui react
```

### Q10. Laravel 6 以後更改 AppName 的方式

由於原本的 ```php artisan app:name NewAppName``` 方式已經在版本 6 被移除
可以安裝 [andrey-helldar/laravel-app](https://github.com/andrey-helldar/laravel-app) 套件達到一樣的功能：

```composer require andrey-helldar/laravel-app```

完成後使用同樣方式更改即可：

```php artisan app:name NewAppName```

### Q11. MacOS 安裝 Laravel 權限問題

執行 ```composer global require laravel/installer``` 若時出現以下錯誤

```./composer.json is not writable.```

先執行以下指令

```ls -lh ~/.composer/composer.json```

如果權限是 `root` 則用以下指令修改(`username`替換為自己的用戶名)

```sudo chown -R username:staff ~/.composer/composer.json```

接著執行安裝可能會遇到如下的 `cache` 資料夾權限問題

```shell
[ErrorException]
  file_put_contents(/Users/username/.composer/vendor/composer/installed.json): failed to open stream: Permission denied
```

更改權限後即可正常安裝

```sudo chown -R $USER ~/.composer/```


## 參考資料

[Cloning a private Github repo](https://stackoverflow.com/questions/2505096/cloning-a-private-github-repo)

[Laravel framework file permission - Security](https://laracasts.com/discuss/channels/general-discussion/laravel-framework-file-permission-security)

[Laravel 5.4 problem - Wrong Route (Not Found) on Ubuntu 16.04](https://www.youtube.com/watch?v=7CatEn5IAlo)

[How To Deploy a Laravel Application with Nginx on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-laravel-application-with-nginx-on-ubuntu-16-04)

[How to install PHP (7 or 7.2) on Ubuntu](https://thishosting.rocks/install-php-on-ubuntu/)

[Changes to Apache httpd.conf not applying on WAMP stack](https://community.bitnami.com/t/changes-to-apache-httpd-conf-not-applying-on-wamp-stack/26224)

[Unable to write in directory Laravel](https://stackoverflow.com/questions/47337374/unable-to-write-in-directory-laravel)

[Mac $PATH 環境變量設置及查詢](https://adon988.logdown.com/posts/7809799-mac-path-environment-variables)

[Laravel 5.4 can't run “php artisan preset react” comand](https://stackoverflow.com/questions/48381322/laravel-5-4-cant-run-php-artisan-preset-react-comand)

[How can I change the application name(space) in Laravel 6](https://stackoverflow.com/questions/58099003/how-can-i-change-the-application-namespace-in-laravel-6)

---

日後遇到其他問題會再持續更新(建立日期：2018/07/10，最後更新日期：2020/10/1)