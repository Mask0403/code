#### Centos7运行级别
> Centos7共有6个运行级别
```
/usr/lib/systemd/system/runlevel0.target -> poweroff.target
/usr/lib/systemd/system/runlevel1.target -> rescue.target
/usr/lib/systemd/system/runlevel2.target -> multi-user.target
/usr/lib/systemd/system/runlevel3.target -> multi-user.target
/usr/lib/systemd/system/runlevel4.target -> multi-user.target
/usr/lib/systemd/system/runlevel5.target -> graphical.targe
/usr/lib/systemd/system/runlevel6.target -> reboot.target
```
- 0代表关机
- 6代表重启
- 1代表救援模式
- 5代表桌面模式
- 2代表多用户模式
- 3代表多用户模式
- 4代表多用户模式

#### 查看系统拥有的运行级别
```
ll /usr/lib/systemd/system/runlevel*.target
```
#### 查看系统当前运行级别
```
systemctl get-default
```
#### 设置系统当前运行级别
```
systemctl set-default multi-user.target
```

#### 系统启动流程
1. 打开硬件电源
2. 开机自检(BIOS
3. 系统引导程序(MBR,GPT
4. 选择GRUB菜单
5. 加载系统内核
6. 初始化PID为1的systemd进程
7. 读取系统运行级别
8. 加载服务配置文件
9. 并行启动系统服务
10. 进入终端