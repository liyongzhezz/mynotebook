## 运行级别

Linux运行级别分为 0~6：

- 0：关机模式；
- 1：单用户模式（破解root密码）；
- 2：无网络支持的多用户模式；
- 3：有网络支持的多用户模式（文本模式，工作中最常用的模式）；
- 4：保留，未使用；
- 5：有网络支持的X-windows支持多用户模式（桌面）；
- 6: 重新引导系统，即重启；



## 启动流程

1. 开机BIOS自检，加载硬盘；
2. 读取MBR引导；
3. grub引导菜单(Boot Loader)；
4. 加载内核kernel；
5. 启动init进程，依据inittab文件设定运行级别；
6. init进程，执行rc.sysinit文件；
7. 启动内核模块，执行不同级别的脚本程序；
8. 执行/etc/rc.d/rc.local；
9. 启动mingetty，进入系统登陆界面。