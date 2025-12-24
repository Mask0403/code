#### ansible基础使用
```
# ansible主要使用免密登录,请提前配置环境
# ansible文档
https://docs.ansible.com/projects/ansible/latest/index.html
```
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
--- 
2. command模块
```
ansible group-name -m command -a 'command'
```
- a后接命令
- command只能使用简单命令,不可加通配符等命令
---
3. shell模块
```
ansible group-name -m shell -a 'command'
```
- -a后接命令,可以使用复杂命令,但可能需要转义
- shell模块用来解决command不能执行复杂命令但依旧有缺陷,可以写成脚本再执行
--- 
4. script模块
```
ansible group-name -m script -a 'script-path'
```
- -a后接脚本路径
- script模块用来解决shell模块执行某些命令不通畅问题
- script流程为先分发脚本到被控端执行再删除脚本
5. file模块
```
ansible group-name -m file -a 'path=/opt/target/ state=directory owner=nfsnobody group=nfsnobody'
```
- file模块用来解决远程文件管理问题
- src表示软链接路径,和state=link使用
- path表示被控端文件目录
- state表示类型
  - directory表示目录类型,通常在路径后加/表示目录
  - touch表示文件类型,file也是文件但是因为不会自动创建而不使用
  - link表示软链接
  - absent表示删除
- mode表示权限
- owner表示属主
- group表示属组
---
6. copy模块
```
ansible group-name -m copy -a 'src=/etc/hosts dest=/opt/ backup=yes'
```
- copy模块用来解决从主控端复制文件到被控端
- src表示主控端文件路径
- dest表示被控端文件位置,加文件名表示且重命名文件
- backup表示遇到重名文件且md5不同时是否创建备份
- mode表示权限
- owner表示属主
- group表示属组
---
7. fetch模块
```
ansible group-name -m fetch -a 'src=/opt/fetch.log dest=/opt/ flat=yes'
```
- fetch用来解决从被控端复制文件到主控端
- src表示被控端文件位置
- dest表示主控端文件位置
- flat表示当重名且md5不同时,是否允许覆盖文件
--- 
8. systemd模块