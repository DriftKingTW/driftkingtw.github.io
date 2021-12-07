---
title: 在 localStorage 儲存布林值(Boolean)
tags:
  - javascript
keywords:
  - javascript
  - localStorage
  - boolean
abbrlink: '63028903'
date: 2021-10-01 16:00:00
---

由於 `localStorage` 規範中只能儲存字串，但如果想儲存布林值或其他格式可利用 `JSON` 的方式達成：

<!--more-->

```javascript
localStorage.setItem("item", true)
let value = localStorage.getItem("item");
console.log(value) // "true"
console.log(JSON.parse(value)) // true
```

# 參考資料

[Cannot set boolean values in LocalStorage?](https://stackoverflow.com/questions/3263161/cannot-set-boolean-values-in-localstorage)
