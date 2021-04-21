---
title: Analyzing logs on CentOS
date: 2019-11-09 23:20:55
tags:
---

### The rsyslog Configuration File
rsyslog文件位于/etc目录下，此文件记录相关log存储的位置。
```
# Include all config files in /etc/rsyslog.d/
$IncludeConfig /etc/rsyslog.d/*.conf

# Turn off message reception via local log socket;
# local messages are retrieved through imjournal now.
$OmitLocalLogging on

# File to store the position in the journal
$IMJournalStateFile imjournal.state

#### RULES ####

# Log all kernel messages to the console.
# Logging much else clutters up the screen.
#kern.*                                                 /dev/console

# Log anything (except mail) of level info or higher.
# Don't log private authentication messages!
*.info;mail.none;authpriv.none;cron.none                /var/log/messages

# The authpriv file has restricted access.
authpriv.*                                              /var/log/secure

# Log all the mail messages in one place.
mail.*                                                  -/var/log/maillog


# Log cron stuff
cron.*                                                  /var/log/cron

# Everybody gets emergency messages
*.emerg                                                 :omusrmsg:*

# Save news errors of level crit and higher in a special file.
uucp,news.crit                                          /var/log/spooler

# Save boot messages also to boot.log
local7.*                                                /var/log/boot.log
```

### The logrotate Configuration File

logrotate配置文件logrotate.conf也位于/etc目录下,此文件管理日志文件很好理解

```
# see "man logrotate" for details
# rotate log files weekly
weekly

# keep 4 weeks worth of backlogs
rotate 4

# create new (empty) log files after rotating old ones
create

# use date as a suffix of the rotated file
dateext

# uncomment this if you want your log files compressed
#compress

# RPM packages drop log rotation information into this directory
include /etc/logrotate.d

# no packages own wtmp and btmp -- we'll rotate them here
/var/log/wtmp {
    monthly
    create 0664 root utmp
	minsize 1M
    rotate 1
}

/var/log/btmp {
    missingok
    monthly
    create 0600 root utmp
    rotate 1
}

# system-specific logs may be also be configured here.
```

### Secure Log

#### Analyzing failed IP

```
sudo grep "Failed password for root" /var/log/secure-xxxxx | awk '{print $11}' | sort | uniq -c | sort -nr | more
```

解读：选出 "Failed password for root"的行，选出第十一个单词，进行一个排序以便于
进行下次的uniq操作，在进行uniq 操作，再按出现的此时进行排序

#### Analyzing failed User
```
sudo grep "Failed password for invalid user" /var/log/secure-20191103 | awk '{print $13}' | sort | uniq -c | sort -nr | more
```
#### About secure
1.修改/etc/ssh/sshd_config文件,修改端口，禁止root登录
```
Port xxxx
PermitRootLogin no
```
2.禁用密码登陆，使用 RSA 私钥登录
