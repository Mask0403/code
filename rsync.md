#### Rsync指令说明
```
rsync -avz -e "ssh -p port" source username@host:path
```
- -a保持原有属性
- -v输出信息
- -z启用压缩
- -e设置额外参数