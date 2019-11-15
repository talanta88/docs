title: 问题解答
---
致所有`walle`用户，为更方便大家使用，精心细致准备了各种常见问题的解决方法，希望能帮助大家快速`trouble shooting`。也请大家为[walle](https://github.com/meolu/walle-web)点个star，为我们的付出点赞，谢谢。

walle运行过程，以及部署过程出错了，具体日志可以查看`logs/runtime.log`，别说你在启动walle的时候没注意到？

Authentication failed.
------------------
```
[work@172.1.2.3]$ mkdir -p /tmp/walle/library
Authentication failed.
```
原因分析：宿主机未与172.1.2.3建立`ssh`信任。好了，来了新的问题，什么是宿主机？答：walle所在的机器

解决方法：把walle与所有目标服务器建立免密码登录
1、命令行（适合机器数量少的），当然也可以用expect脚本批量
```
[walle]$ ssh-copy-id -i ~/.ssh/id_rsa.pub work@172.1.2.3
# 输入 work@172.1.2.3 密码
```

2、手工复制粘贴
```
[walle]$ cat ~/.ssh/id_rsa.pub #复制
[work@172.1.2.3]$  vi ~/.ssh/authorized_keys #粘到最后面
```

如果问题仍然没有得到解决，请确认免密码登录是否成功
```
[walle]$ ssh work@172.1.2.3
```

免密码登录需要远程机器权限满足以下三个条件：
```
~ 755
~/.ssh 700
~/.ssh/authorized_keys 644 或 600
```


Can't assign requested address
------------------------------
```
socket.error: [Errno 49] Can't assign requested address: ('admin.walle-web.io', 5000)
```
原因分析：服务启动时连接域名或ip失败
解决方法：nginx、hosts配置、setting_prod.py的域名配置一致，且ping通。

99.9%的人都会粗心，你不是第一个，也不会是最后一个。

Centos7 安装了python2.7、python3.6，导致walle脚本init时，无法启动gunicorn
--------------------
原因：gunicorn 需要python 3.4+，而当前系统运行的版本是python2.7
解决办法：
  1、/usr/bin/python 升级到3.4+,通过mv /usr/bin/python /usr/bin/python.old
  2、ln -s /usr/bin/python3.6 /usr/bin/python (升级当前环境的python至3.6)
  3、rm -rf walle-web (把walle文件目录删除)
  4、git clone https://github.com/meolu/walle-web.git (重新远端拉取)
  5、sh admin.sh init
  6、yum install -y python3-devel (若出现mysqlclient等缺少依赖时，请执行)
  
  
  
  
