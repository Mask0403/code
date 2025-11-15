#### 生成指定大小文件
```
dd if=/dev/zero of=/tmp/1g bs=1M count=1024
```
- if=input  file  > 文件从哪来
- of=output file  > 文件到哪去
- bs=block size   > 缓冲大小
- count=从输入文件复制多少个"块"到输出文件