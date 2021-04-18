---
title: Quick-Fix node-sass no such file or directory error
tags:
  - vue
  - node-sass
keywords:
  - vue
  - node-sass
abbrlink: c7e83e15
date: 2021-04-18 00:30:00
---

Q: Recently I run into this error whe create a vue project via `Vue-Cli` :

```shell
 ERROR  Failed to compile with 1 error                             

 error  in ./src/App.vue?vue&type=style&index=0&lang=scss&

Syntax Error: Error: ENOENT: no such file or directory, scandir '/***/node_modules/node-sass/vendor'
```

<!--more-->

A: Simply rebuild wil do the tirck：`npm rebuild node-sass`

# Reference

[Error: ENOENT: no such file or directory, scandir '**/node_modules/node-sass/vendor' #1579](https://github.com/sass/node-sass/issues/1579)