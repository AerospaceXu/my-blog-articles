# Linux 入门（一）

本文写于 2020 年 1 月 11 日

在系统开启时，操作系统会帮助我们启动很多程序，在 Windows 中叫做 **Service**（服务），而在 Linux 中叫做 **Daemon**（守护进程）。

一般来说，Linux 的登陆方法有三种：

1. 命令行登陆
2. SSH 登陆
3. 图形界面登陆

登陆需要一个账户和对应的密码——root 用户享有最高权限。

## 几个常用命令

```bash
# 关机
shutdown

# 十分钟后关机
shutdown -h 10
shutdown -h +10

# 现在关机
shutdown -h now

# 今天 8 点 50 关机
shutdown -h 20:50

# 立即重启系统
shutdown -r now
```

（未完待续……）
