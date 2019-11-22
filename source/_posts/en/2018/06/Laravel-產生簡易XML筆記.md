---
title: Laravel 產生簡易XML筆記
tags:
  - PHP
  - Laravel
  - XML
keywords:
  - PHP
  - Laravel
  - XML
abbrlink: fe7b8274
date: 2018-06-26 16:36:12
---

最近在寫串接功能時，遇到需要使用Laravel動態產生XML的功能，完成後做個筆記，除此之外也能拿來產生網站組織架構的Sitemap，讓Google等搜尋引擎能夠更有效率的爬你的網站頁面。<!--more-->

# How

假設要使用Laravel產生如下的簡易產品清單XML：

```XML
<?xml version="1.0" encoding="UTF-8"?>
<products>
    <Product>
        <Name>Product_1</Name>
        <Description>Product_Description</Description>
        <URL>https://mywebsite.com/Product_URL/</URL>
        <Price>1000</Price>
    </Product>
    <Product>
        <Name>Product_2</Name>
        <Description>Product_Description</Description>
        <URL>https://mywebsite.com/Product_URL/</URL>
        <Price>1500</Price>
    </Product>
</products>
```

首先在 `resources/views` 裡建立一個 `product_list.blade.php` 視圖，用來顯示XML的內容 ("product_list"名稱可自訂)。

內容如下：

```XML
<?xml version="1.0" encoding="UTF-8"?>
<products>
    <!-- 利用foreach印出所有產品資訊 -->
    @foreach($products as $product)
    <Product>
        <Name>{{ $product->name }}</Name>
        <Description>{{ $product->description }}</Description>
        <!-- 利用 Request::root() 取得網站URL -->
        <URL>{{ Request::root() }}/product/{{ $product->id }}</URL>
        <Price>{{ $product->price }}</Price>
    </Product>
    @endforeach
</products>
```

然後建立一個Controller(名稱自訂，這裡範例使用ProductController，跟處理顯示商品同樣的控制器)。
在Controller裡建立Function用來取得資料並傳送給View裡的視圖顯示。

```php
public function productList() {
    // 取得商品資訊, 並以id作降冪排序
    $products = Product::orderBy('id', 'DESC')->get();
    // 將資料交給product_list.blade.php產生XML內容
    return response()->view('product_list', compact('products'))->header('Content-Type', 'text/xml');
}
```

最後在`route/web.php`裡加上路徑 
```php 
Route::get('/product_list.xml', 'ProductController@productList');
```

到這邊大功告成，打上網域名+`/product_list.xml`就可以查看XML所產生的XML了！

## 注意事項

如果像此方法利用Blade模板產生XML要注意在PHP的`php.ini` 設定中 ***Short Open Tag (<?)*** 選項要關閉，如果必須打開此選項的話可以改用以下寫法來產生XML Header：

```php
<?php echo '<?xml version="1.0" encoding="UTF-8"?>'; ?>
```

## 參考資料

[Generate a Simple XML Sitemap Using Laravel](https://laraget.com/blog/generate-a-simple-xml-sitemap-using-laravel)