---
layout: post
title:  "brew install mysql on mac os 10.10"
date:   2014-10-27 11:40:00
categories: tool
tags: mac
author: "Victor"
---

### Step-by-step

```bash
brew remove mysql
brew cleanup
launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
rm ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
sudo rm -rf /usr/local/var/mysql
brew install mysql
unset TMPDIR
mysql_install_db --verbose --user=`whoami` --basedir="$(brew --prefix mysql)" --datadir=/usr/local/var/mysql --tmpdir=/tmp
```

然后进行安全设置，注意下面命令中的 mysql 版本要和目前安装的版本一致

```bash
/usr/local/Cellar/mysql/5.5.10/bin/mysql_secure_installation
```

用下面的命令自动在系统启动的时候加载 mysql

```bash
#start
launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
#stop
launchctl unload -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
```

下面两个命令可以给你提供很大的帮助

```bash
brew info mysql
mysql --help
```

### 配置 my.cnf

如果找不到 /etc/my.cnf 也不用怕，自己创建一个，然后复制如下内容即可

```
[client]
port = 3306
socket = /tmp/mysql.sock
default-character-set = utf8

[mysqld]
collation-server = utf8_unicode_ci
character-set-server = utf8
init-connect ='SET NAMES utf8'
max_allowed_packet = 64M
bind-address = 127.0.0.1
port = 3306
socket = /tmp/mysql.sock
```

### 相关链接

* [brew install mysql on mac os](http://stackoverflow.com/questions/4359131/brew-install-mysql-on-mac-os)
* [mac os 安装 mysql总是出现cannot find mysql.sock](https://ruby-china.org/topics/794)
* [在 Mac 下用 Homebrew 安装 MySQL](http://blog.neten.de/posts/2014/01/27/install-mysql-using-homebrew/)
* [Disable Services in OSX (services.msc)](http://apple.stackexchange.com/questions/105892/disable-services-in-osx-services-msc)
