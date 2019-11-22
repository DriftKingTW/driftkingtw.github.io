---
title: Hexo Github deploy 問題修正
tags:
  - hexo
  - Github
abbrlink: 286b8c2b
date: 2018-03-03 13:38:43
keywords:
  - Github
  - 問題
  - hexo
---

今天用另一台電腦寫blog要用Powershell部屬到Github上時發生了錯誤

錯誤訊息如下<!--more-->
```bash
PS C:\Users\DriftKingTW\blog> hexo d
INFO  Deploying: git
INFO  Clearing .deploy_git folder...
INFO  Copying files from public folder...
INFO  Copying files from extend dirs...
On branch master
nothing to commit, working tree clean
fatal: HttpRequestException encountered.
   An error occurred while sending the request.
bash: /dev/tty: No such device or address
error: failed to execute prompt script (exit code 1)
fatal: could not read Username for 'https://github.com': No error
FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html
Error: fatal: HttpRequestException encountered.
   An error occurred while sending the request.
bash: /dev/tty: No such device or address
error: failed to execute prompt script (exit code 1)
fatal: could not read Username for 'https://github.com': No error

    at ChildProcess.<anonymous> (C:\Users\DriftKingTW\blog\node_modules\hexo-util\lib\spawn.js:37:17)    at emitTwo (events.js:126:13)
    at ChildProcess.emit (events.js:214:7)
    at ChildProcess.cp.emit (C:\Users\DriftKingTW\blog\node_modules\cross-spawn\lib\enoent.js:40:29)
    at maybeClose (internal/child_process.js:925:16)    at Process.ChildProcess._handle.onexit (internal/child_process.js:209:5)
```

上網翻了不少資料, 一些git設定跟ssh設定也都測試過了依然無法解決問題
後來使用了另一種設定方法

原本的_config.yml
```yaml
deploy: 
  type: git
  repo: https://github.com/username/your_repo.git
  branch: 
```

修改成
```yaml
deploy: 
  type: git
  repo: git@github.com:username/your_repo.git
  branch: 
```

就能夠正常Deploy, 不過在原本架設hexo的電腦上是能夠使用第一種方法的
這裡放上我的解決方法, 提供參考

參考資料:
[Hexo + Github 簡單使用教學](https://wwssllabcd.github.io/blog/2014/12/22/how-to-install-hexo/#%E6%8A%8A-Blog-%E6%94%BE%E5%9C%A8-Github-%E4%B8%8A)