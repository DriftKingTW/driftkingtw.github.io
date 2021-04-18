---
title: Quick-Fix node-sass no such file or directory 錯誤
tags:
  - vue
  - node-sass
keywords:
  - vue
  - node-sass
abbrlink: 89709d60
date: 2021-04-18 00:30:00
---

Q: 最近在利用 `Vue-Cli` 建立專案時遇到 `node-sass` 出現如下錯誤：

```shell
 ERROR  Failed to compile with 1 error                             

 error  in ./src/App.vue?vue&type=style&index=0&lang=scss&

Syntax Error: Error: ENOENT: no such file or directory, scandir '/***/node_modules/node-sass/vendor'
```

<!--more-->

A: Rebuild 即可：`npm rebuild node-sass`

# 參考

[Error: ENOENT: no such file or directory, scandir '**/node_modules/node-sass/vendor' #1579](https://github.com/sass/node-sass/issues/1579)