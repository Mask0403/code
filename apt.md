#### 在没有安装软件的情况下,只知道命令该怎么处理
在redhet系列中非常简单,只需要使用`yum provides target`便能返回软件包名
但是debian/ubuntu该怎么处理呢,很遗憾没有原生处理的方式,只能通过间接的处理

- 1.通过在线网站https://packages.ubuntu.com/查询
- 2.第三方软件apt-file

`apt-file search target`但是可能会返回大量内容和路径,通过grep能大幅减少无效内容`apt-file search target|grep "/usr/bin/*"`,但结果仍有无效内容掺杂或者路径不同.需要手动确认
