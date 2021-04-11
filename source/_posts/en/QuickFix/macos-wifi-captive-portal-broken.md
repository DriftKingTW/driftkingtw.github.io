---
title: Quick Fix - macOS WiFi Captive Portal Broken
tags:
  - Quick Fix
  - MacOS
  - WiFi
keywords:
  - Quick Fix
  - MacOS
  - WiFi
  - Captive Portal
abbrlink: ae6b5c87
date: 2021-04-11 19:42:36
---

Q: Why I got error on captive portal wifi?

A: Create a file `my.shim.catalina.captivenetworkassistant.plist` in `~/Library/LaunchAgents`

<!-- more -->

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

Then run the command down below:

```shell
cd ~/Library/LaunchAgents

launchctl load my.shim.catalina.captivenetworkassistant.plist

launchctl start my.shim.catalina.captivenetworkassistant
```

Restart the macOS, the issue should be solved 👌

If you want to remove this daemon:

```shell
cd ~/Library/LaunchAgents

launchctl stop my.shim.catalina.captivenetworkassistant

launchctl unload my.shim.catalina.captivenetworkassistant.plist

rm my.shim.catalina.captivenetworkassistant.plist
```

{% colorquote info %}
The post I reference says that this issue fixed on `10.15.1` update, but I still got this error on `11.2.3` 😭
{% endcolorquote %}

> Quick Fix is a series of my solution for some random problem

# Reference

[macOS Catalina WiFi issue — captive portal broken](https://poweruser.blog/macos-catalina-wifi-issue-captive-portal-broken-45610cc016b5)