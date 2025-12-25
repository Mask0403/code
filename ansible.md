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
```
ansible group-name -m systemd -a 'name=firewalld state=stopped'
```
- name表示服务名称
- state表示状态
  - started表示开启
  - stopped表示关闭
  - restarted表示重启,先关闭再开启
  - reloaded表示重新载入配置文件,需要服务支持
- enabled表示是否开机自启
- daemon-reload表示重新加载服务的service配置文件
---
9. yum模块
```
ansible group-name -m yum -a 'name=tree state=present'
```
- yum模块不仅仅适用于yum软件包管理,还可用于apt软件包管理
- name表示软件包名称
- state表示状态
  - installed=present,表示安装,为默认选项
  - removed=absent,表示卸载
  - lastest表示更新或安装
---
10. get_url模块
```
ansible group-name -m get_url -a 'url=https://nodejs.org/dist/v24.12.0/node-v24.12.0-linux-x64.tar.xz dest=/opt/node/'
```
- get_url用来代替wget和curl
- url表示链接
- dest表示下载目录,get_url不会自动创建不存在的目录,没有对应则执行失败
- mode表示权限
- owner表示属主
- group表示属组
---
11. group模块
```
ansible group-name -m group -a 'name=www gid=52253 state=present'
```
- name表示组名称
- gid表示组id
- state表示状态
  - present表示创建
  - absent表示删除
- system暂时不清楚特性
---
12.  user模块
```
ansible group-name -m user -a 'name=www group=www uid=52253 shell=/sbin/nologin create_home=no state=present'
```
- name表示用户名
- group表示组名
- groups表示附加组
- uid表示用户id
- shell表示是否允许登录
  - /bin/bash,允许登录
  - /sbin/nologin,不允许登录
- create_home表示是否创建用户家目录,即使是no也会显示创建
- state表示状态
  - present表示创建
  - absent表示删除
- password表示密码,但不能是明文密码
  - {{ 'mypassword' | password_hash('sha512', 'mysecretsalt') }}
--- 
13. mount模块
```
ansible group-name -m mount -a 'src=172.16.10.131:/nfs/ path=/nfs/ fstype=nfs opts=noexec,nosuid,nodev state=present'
```
- src表示是从哪里挂载,比如nfs-server
- path表示是到哪里挂载,本地挂载点
- fstype表示挂载什么类型
  - xfs,nfs,iso9660(光盘)
- opts表示挂载选项
- state表示状态
  - mounted表示挂载并修改/etc/fstab文件
  - unmount表示卸载但不修改/etc/fstab文件
  - absent表示卸载并修改/etc/fstab文件
  - remounted表示重新挂载
  - present表示仅修改/etc/fstab,但不挂载(不常用)
---
14. cron模块
```
ansible group-name -m cron -a 'name="echo some" minute="*/3" job="/usr/bin/echo 123 >>/opt/123.log" state=present'
```
- cron模块格式比较严格
- name表示定时任务名称,并且会自动在开头添加Ansible:,删除根据前缀加名称定位
- 运行时间根据设置的时间运行
  - minute,每三分钟>minute="*/3"
  - hour
  - day
  - month
  - week
- job表示执行的任务,其中的命令要用绝对路径
- state表示状态
  - present表示安装
  - absent表示卸载