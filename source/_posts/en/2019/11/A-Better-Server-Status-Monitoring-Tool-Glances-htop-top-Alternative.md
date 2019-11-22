---
title: A Better Server Status Monitoring Tool - Glances (htop/top Alternative)
tags:
  - RaspberryPi
  - StatusMonitor
keywords:
  - RaspberryPi
  - StatusMonitor
abbrlink: 598226a2
lang: en
date: 2019-11-18 19:50:00
---

Recently, I'm looking for a temperture and cpu status monitor for my Pi nas server. `vcgencmd` command and `htop` get the jobs done, but it's always better to have them in the same window (`htop` can't show temperture which bothers me alot). So I found an cross-platform, python-based `htop` alternative - [Glances](https://nicolargo.github.io/glances/), it's easy to install and super handy. The only downside is that it uses a bit more cpu resources than I expected (on Pi4 4GB version).

# Installation

In Linux (Debian, Ubuntu, etc.) you can simply use `apt-get` to install.

`sudo apt-get install glances`

If you have `Homebrew` installed on MacOS, use `brew` to install.

`brew install glances`

Note that I have run into some trouble when installing on MacOS, it shows the error message like this:

```shell
Traceback (most recent call last):
  File "/usr/local/Cellar/glances/3.1.3/libexec/bin/glances", line 6, in <module>
    from pkg_resources import load_entry_point
ModuleNotFoundError: No module named 'pkg_resources'
```

After googling, post-install should solved the problem.

`brew postinstall python3`

Windows need to install `python` first, and use `pip` to get `glances`

`pip install glances`

# Usage

## Host mode

Open Terminal and run glances

`glances`

Show RAW JSON status

`glances --stdout cpu.user,mem.used,load`

or in CSV format

`glances --stdout-csv now,cpu.user,mem.used,load`

Enter `q` or `Esc` to quit

## Server/Client Mode

Server side:

`glances -s`

Client side (change SERVER-IP to your own server ip):

`glances -s SERVER-IP`

## Remote mode (Web-UI)

Server side:

`glances -w`

Client side (change SERVER-IP to your own server ip, default port `61208`):

`http://SERVER-IP:61208`

Done! Now you can monitoring all status (including cpu-temperture) in comfort! (Image shows my Pi nas' status)

![](https://res.cloudinary.com/driftkingtw/image/upload/f_auto/v1574080130/blog/2019/11/%E9%9B%BB%E8%85%A6%E4%BC%BA%E6%9C%8D%E5%99%A8%E8%B7%A8%E5%B9%B3%E8%87%BA%E7%9A%84%E9%96%8B%E6%BA%90%E7%9B%A3%E6%8E%A7%E5%A5%BD%E5%B9%AB%E6%89%8B%20-%20Glances/Screen_Shot_2019-11-18_at_7.54.46_PM.png)


# Reference

[Glances](https://nicolargo.github.io/glances/)

[fails after brew installation: ModuleNotFoundError: No module named 'pkg_resources' #260](https://github.com/asciinema/asciinema/issues/260)