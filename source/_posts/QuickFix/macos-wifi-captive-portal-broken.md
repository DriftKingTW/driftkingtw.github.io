---
title: Quick Fix - macOS 連接公用 WiFi 出現登入錯誤訊息
tags:
  - Quick Fix
  - MacOS
  - WiFi
keywords:
  - Quick Fix
  - MacOS
  - WiFi
  - Captive Portal
  - 錯誤
abbrlink: 238031f6
date: 2021-04-11 19:42:36
---

Q: macOS 連接公用 WiFi 登入畫面一片空白並出現登入錯誤訊息？

A: 在 `~/Library/LaunchAgents` 中新增一個檔案名為 `my.shim.catalina.captivenetworkassistant.plist`

<!-- more -->

內容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN" \
"http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
 <dict>
  <key>Label</key>
  <string>my.shim.catalina.captivenetworkassistant</string>
  <key>LowPriorityIO</key>
  <true/>
  <key>ProgramArguments</key>
  <array>
   <string>open</string>
   <string>/System/Library/CoreServices/Captive Network Assistant.app</string>
  </array>
  <key>WatchPaths</key>
  <array>
   <string>/Library/Preferences/SystemConfiguration</string>
  </array>
  <key>RunAtLoad</key>
  <true/>
 </dict>
</plist>
```

接著依序執行如下指令

```shell
cd ~/Library/LaunchAgents

launchctl load my.shim.catalina.captivenetworkassistant.plist

launchctl start my.shim.catalina.captivenetworkassistant
```

重啟電腦即可解決問題 👌

若要刪除此背景服務：

```shell
cd ~/Library/LaunchAgents

launchctl stop my.shim.catalina.captivenetworkassistant

launchctl unload my.shim.catalina.captivenetworkassistant.plist

rm my.shim.catalina.captivenetworkassistant.plist
```

{% colorquote info %}
我參考的解決文章中提到 `10.15.1` 版本中已解決此問題，但我 `11.2.3` 版本仍遇到此問題 😭
{% endcolorquote %}

> Quick Fix 是一些我自己平時遇到各種小問題的合輯，並提供自己的快速解法

# 參考

[macOS Catalina WiFi issue — captive portal broken](https://poweruser.blog/macos-catalina-wifi-issue-captive-portal-broken-45610cc016b5)