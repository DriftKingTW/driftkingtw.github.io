---
title: 在 macOS 上安裝 Ruby
tags:
  - MacOS
  - Ruby
keywords:
  - MacOS
  - Ruby
  - gem
abbrlink: a6c92b79
date: 2021-04-02 21:00:00
---

雖然 macOS 已預裝了 Ruby ，但我在使用 `gem install` 時遇到了個問題：

```
You don't have write permissions for the XXX directory.
```

後來發現是 macOS 不讓使用者更變預設 Ruby 相關的玩意，用 `sudo` 去跑安裝當然也不是個選項，所以還是另外獨立安裝一個，再把 `PATH` 設定到另一個 Ruby 比較方便。

<!-- more -->

## 獨立安裝方法

利用 Homebrew 安裝 `chruby` 與 `ruby-install` （此方法屆時可自由切換所需要的 Ruby 版本）

```shell
brew install chruby ruby-install
```

接著用 `ruby-install` 安裝 Ruby：

```shell
ruby-install ruby
```

`ruby-install` 會自動使用 Homebrew 安裝各種需要的 dependencies，然後從 source 編譯 Ruby，所以需要等個一陣子，可以先去泡壺茶休息下 🫖

安裝完後在 `~/.zshrc` 或 `~/.bashrc` 中新增以下兩條路徑（看自己用的是那種 shell，macOS Catalina 之後預設皆為 zsh ）

```bash
source /usr/local/opt/chruby/share/chruby/chruby.sh
source /usr/local/opt/chruby/share/chruby/auto.sh
```

完成後重啟 Terminal 輸入 `which ruby`，若*不是*顯示 `/usr/bin/ruby` 則代表預設的 Ruby 已經不是 macOS 預設的了！

接著我們可以利用 `chruby` 選擇 ruby 版本：

```shell
chruby 3.0.0
```

提供給遇到相同問題的朋友們參考 😉

# 參考

[How to Install Ruby on a Mac with chruby, rbenv, or RVM](https://blog.engineyard.com/how-to-install-ruby-on-a-mac-with-chruby-rbenv-or-rvm)

[You don't have write permissions for the /Library/Ruby/Gems/2.3.0 directory. (mac user)](https://stackoverflow.com/questions/51126403/you-dont-have-write-permissions-for-the-library-ruby-gems-2-3-0-directory-ma)


