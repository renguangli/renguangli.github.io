```base
运行事务
  准备中  :                                                                                                                                                                          1/1 
  安装    : chkconfig-1.20-2.el9.x86_64                                                                                                                                                      1/1 
Error unpacking rpm package chkconfig-1.20-2.el9.x86_64
  验证    : chkconfig-1.20-2.el9.x86_64                                                                                                                                                      1/1 
失败:
  chkconfig-1.20-2.el9.x86_64                                                                                                                                                                    
错误：事务失败
```



解决办法

```bash
rm /etc/init.d
```

安装

```bash
yum install chkconfig
```

