#### nfs-server
1. 安装软件包
```
yum install -y rpcbind nfs-utils
```
2. 设置共享目录,更改所有者
```
mkdir /nfs131/
# 安装nfs-utils后自动添加nfsnobody用户
chown -R nfsnobody:nfsnobody /nfs131/
```
3. 修改nfs配置文件
```
# 修改的配置文件为/etc/exports,实际配置文件为/var/lib/nfs/etab
echo '/nfs131/   172.16.10.0/24(rw)' >/etc/exports
chmod 600 /etc/exports
```
4. 启动服务
```
# 优先启动rpcbind
systemctl start rpcbind
rpcinfo -p
systemctl start nfs
```
5. 设置开机自启
```
systemctl enable rpcbind
systemctl enable nfs
```
#### nfs-client
1. 安装软件包,但不必启动服务
```
yum install -y nfs-utils
```
2. 设置挂载点,更改所有者
```
mkdir /nfs131/
chown -R nfsnobody:nfsnobody /nfs131/
mount -t nfs 172.16.10.131:/nfs131/ /nfs131/
```
3. 永久挂载
`永久挂载后要优先启动nfs服务端`
```
# 有两种方式,rc.local和fstab,优先fstab
echo '172.16.10.131:/nfs/ /nfs/                                 nfs     defaults        0 0' >> /etc/fstab
# rc.local只需要将挂载命令写入/etc/rc.local中,再将/etc/rc.d/rc.local赋予执行权限即可
```
`如果永久挂载后没有优先启动nfs服务端,则需等待几分钟`