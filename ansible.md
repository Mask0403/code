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
# 主机清单文件位置在/etc/ansible/hosts,可以使用ini格式和yaml,推荐yaml简洁明了,yaml不能使用tab进行缩进,以及:属性后要接一个空格
# ini
[test]
test241
domain
ip
# 将多个组集中在一起
[group-name:children]
collection-name
```
---
```
# yaml
test:
  hosts:
    test241
    domain:
    ip:
      nsible_host: host
      ansible_user: user
      ansible_password: passwd
      ansible_ssh_private_key_file: key--file
# 将多个组集中在一起
new-group:
  children:
    group-name
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