#### nfs
1. 安装软件包
```
yum install -y rpcbind nfs-utils
```
2. 设置共享目录,更改所有者
```
mkdir /nfs
# 安装nfs-utils后自动添加nfsnobody用户
chown -R nfsnobody:nfsnobody /data
```
3. 修改nfs配置文件
```
# 修改的配置文件为/etc/exports