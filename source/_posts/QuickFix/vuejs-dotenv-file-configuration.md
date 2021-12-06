---
title: Vue.js dotenv 環境變數使用及注意事項
tags:
  - vue
  - dotenv
keywords:
  - vue
  - dotenv
abbrlink: e7ecd15e
date: 2021-08-18 16:00:00
---

最近遇到了 API URL 分 Development 及 Production 不同設定的問題，想使用環境變數來解決，讓 URL 在不同的環境下自動使用對應的設定，嘗試後發現有些使用重點跟容易忽略的地方：

<!-- more -->

- Vue 使用的是 `.env` 格式儲存環境變數，需要放置在專案根目錄(非 `/src`)。
- 變數名稱需要以 `VUE_APP_` 當作開頭。
- 需要重啟 Development Server 才會生效。
- 舊版的 Vue 設定路徑為 `config/` 下。
- 可透過 `.env.development` `.env.production` `.env.test` 等方式在不同情況下載入不同的設定，若是 `.env` 則所有情況下都會載入相關設定。

# 參考資料

[Vue-cli 3 Environment Variables all undefined](https://stackoverflow.com/questions/55510326/vue-cli-3-environment-variables-all-undefined)

[https://cli.vuejs.org/guide/mode-and-env.html](https://cli.vuejs.org/guide/mode-and-env.html#environment-variables)