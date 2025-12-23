#### ansible基础使用
`ansible主要使用免密登录,请提前配置环境`
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
[test]
test241
ip
domain
# 将多个组集中在一起
[group-name:children]
collection-name
```
--- 
#### ansible模块
`ad-hoc为命令行模式,playbook为剧本模式`
1. ping模块
```
ansible group-name -m ping
```
- group-name为分组名称
- -m指定模块
- success表示执行成功