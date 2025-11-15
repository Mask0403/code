#### 生成指定大小文件
```
dd if=/dev/zero of=/tmp/1g bs=1M count=1024
```
- if=input  file  > 文件从哪来
- of=output file  > 文件到哪去
- bs=block size   > 缓冲大小
- count=从输入文件复制多少个"块"到输出文件


#### 设置swap
1. dd生成要设置大小的临时文件
2. mkswap /tmp/1g,将文件格式化为swap文件
3. chmod 600,将文件设置为只读
4. 挂载swap有两种方式
   - swapon /tmp/1g,
   - 在/etc/fstab中写入/tmp/1g swap swap defaults 0 0