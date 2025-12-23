#### ansible
1. 下载软件包,修改配置
```
yum install -y ansible
# 配置文件在/etc/ansible/ansible.cfg
host_key_checking = False
log_path = /var/log/ansible.log
```
- host_key_checking忽略yes/no
- log_path日志位置
--- 
2. 设置主机清单
```
# 主机清单文件位置在/etc/ansible/hosts

```