#### rsync指令说明
```
rsync -avz -e "ssh -p port" --password-file=path --exclude=file --delete source-file username@host::module 
```
- -a保持原有属性
- -v输出信息
- -z启用压缩
- -e设置额外参数
- --password-file密码文件
- --exclud排除文件
- --delete同步一致

`注意点`rsync中文件夹结尾带不带/
- 带/表示仅复制该目录所有文件,/etc/
- 不带/表示复制该目录,eg./etc


#### rsync-server
```
# rsyncd.conf
fake super =yes
uid = rsync
gid = rsync
use chroot = no
max connections = 2000
timeout = 600
pid file = /var/run/rsyncd.pid
lock file = /var/run/rsync.lock
log file = /var/log/rsyncd.log
ignore errors
read only = false
list = false
#hosts allow = ipaddress/netmask
#hosts deny = ipaddress/netmask
auth users = username
secrets file = /etc/rsync.server
#####################################
[data]
comment = data module‌
path = /data
```
```
echo 'username:passwd' > /etc/rsync.server
chmod 600 /etc/rsync.server
useradd -s /sbin/nologin -M rsync
mkdir /data/
chown rsync:rsync /data
systemctl enable rsyncd
systemctl start rsyncd
```