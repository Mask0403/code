#### 免密登录
`免密登录只是单向,并未双向`
##### 免密登录1.0
1. client生成密钥
```
ssh-keygen -t rsa
```
2. client分发公钥到server
```
ssh-copy-id -i ~/.ssh/id_rsa.pub root@test241
```

##### 免密登录2.0
- 1.0虽然简单但是不能算作免密登录,因为还有部分操作需要手动操作
- 生成密钥时需要手动确认密钥位置和设置二次密码
- 分发公钥时也需要手动同意和输入密码

1. client生成密钥
```
ssh-keygen -t rsa -f ~/.ssh/id_rsa -P ''
```

2. client分发公钥到server
```
ssh-copy-id -i ~/.ssh/id_rsa.pub root@test241
```

##### 免密登录3.0
- 2.0虽然解决了自动生成密钥,但还是分发公钥还是需要手动操作
- 分发公钥时需要手动同意和输入密码
1. client生成密钥
```
ssh-keygen -t rsa -f ~/.ssh/id_rsa -P ''
```
- -t指定加密算法
- -f指定密钥生成路径
- -P指定二次密码

2. client分发公钥到server
```
yum install -y sshpass
sshpass -p '123' ssh-copy-id -i ~/.ssh/id_rsa.pub root@test241 -o StrictHostKeyChecking=no
```
- sshpass用来解决输入密码
- -i指定公钥文件,!!!注意,文件名请不要随意修改eg>id_rsa修改成id-rsa,否则会引起bug
- -o添加额外参数,StrictHostKeyChecking=no表示忽略检查,用来解决yes/no问题