#### ansible-playbook
1. ansible-playbook通用模板
```
---
- name: desc
  hosts: group-name
  tasks:
    # task01
    - name: desc
      full-name of modlues:
        options
      tag: desc
      
    # task02
    - name: desc
      full-name of modlues:
        options
      tag: desc
...
```

2. ansible-playbook内部变量
```
---
- name: test vars in playbook file
  hosts: tmp
  vars: 
    dir: /tmp/vars/
    file: readme
  tasks:
    - name: make dir
      ansible.builtin.file:
        path: "{{dir}}"
        state: directory
    - name: make file
      ansible.builtin.file:
        path: "{{dir}}{{file}}"
        state: touch
...
```
- 内部变量写在第一层,变量仅作用于该文件
- 取变量用{{}}符号,如果取值在某行开头则需要用""包裹
3. 文件变量
```
---
- name: test vars in playbook file
  hosts: tmp
  vars_files: ./file-vars.yml
  tasks:
    - name: make dir
      ansible.builtin.file:
        path: "{{dir}}"
        state: directory
    - name: make file
      ansible.builtin.file:
        path: "{{dir}}{{file}}{{postfix}}"
        state: touch
...
```
```
# file-vars.yml
dir: /tmp/vars/03/
file: readme
postfix: .txt
```
- 文件变量只需要在剧本中引用就行了
4. 分组变量
```
---
- name: test vars in playbook file
  hosts: tmp
  tasks:
    - name: make dir
      ansible.builtin.file:
        path: "{{dir}}"
        state: directory
    - name: make file
      ansible.builtin.file:
        path: "{{dir}}{{file}}{{postfix}}"
        state: touch
...
```
```
group_vars/
└── tmp
    └── file-vars.yml
```
- 分组变量只需要将文件变量放入分组文件夹中即可,自动引用
5. facts变量
```
---
- name: print facts vars
  hosts: tmp
  tasks:
    - name: use debug
      ansible.builtin.debug:
        msg: 
          ip is {{ansible_default_ipv4.address}}
          hostname is {{ansible_hostname}}
          os is {{ansible_distribution}}
          date is {{ansible_date_time.date}}
...
```
- facts变量在ansible.builtin.setup模块中
- 如果不需要facts变量,可以禁用来加快剧本运行速度
```
# 禁用facts变量
---
- name: print facts vars
  hosts: tmp
  gather_facts: false
  tasks:
    - name: use debug
      ansible.builtin.debug:
        msg: 
          there is no fatcs
...
```
6. jinja2与template
```
---
- name: use j2 and template
  hosts: tmp
  tasks:
    - name: use template
      ansible.builtin.template:
        src: ./template/simple-vars.j2
        dest: /tmp/target
        backup: true
...
```
```
template/
└── simple-vars.j2
```
```
dir: /tmp/{{ansible_hostname}}/
file: {{ ansible_distribution }}
```
```
# cat target
dir: /tmp/556fe1635e88/
file: Ubuntu
```
- j2中的取值不需要""包裹
7. register变量
```
---
- name: test
  hosts: tmp
  gather_facts: false
  tasks:
    - name: use shell
      ansible.builtin.shell:
        cmd: hostname
      register: result
    - name: use debug
      ansible.builtin.debug:
        msg: hostname is {{result.stdout}}
...
```
- register也只能作用于单剧本中
- register用来存储命令执行结果
- 取变量结果为var-name.stdout
--- 
#### ansible监听器
```
---
- hosts: test
  gather_facts: false
  tasks:
    - name: copy file notify
      ansible.builtin.copy:
        src: ./template/target
        dest: /tmp/target
        backup: true
      notify:
        - turn
  handlers:
    - name: turn
      ansible.builtin.debug:
        msg: handler1 start
...
```
- notify与模块平级
- handlers不是handler
- handlers与tasks平级
---
#### ansible流程控制
1. when判断
```
---
- name: switch yum apt
  hosts: all
  tasks:
    - name: match redhat
      ansible.builtin.debug:
        msg: this is Centos
      when: ansible_distribution is match("Centos")
    - name: match Ubuntu
      ansible.builtin.debug:
        msg: thisi is Ubuntu
      when: ansible_distribution is match("Ubuntu")
...
```
- when与模块平级
2. loop循环
```
---
- name: test loops
  hosts: all
  gather_facts: false
  tasks:
    - name: loop
      ansible.builtin.debug:
        msg: "{{item}}"

      loop:
        - text
        - audio
...
```
- 是loop不是loops
- loop与模块平级
3. with_items循环
```
---
- name: test loops
  hosts: all
  gather_facts: false
  tasks:
    - name: loop
      ansible.builtin.debug:
        msg: "{{item}}"

      with_items:
        - text
        - audio
...
```
- 是with_items不是with_item
- with_items与模块平级
--- 
#### ansible文件包含
1. include文件包含
```
# 13-include.yml
---
- name: test loops
  hosts: all
  tasks:
    - tags: include
      include_tasks:
        file: ./13-sub.yml

...
```
```
# 13-sub.yml
- name: loop
  ansible.builtin.debug:
    msg: "{{item}}"
  with_items:
    - text
    - audio
```
