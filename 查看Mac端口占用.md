## Mac OS/Linux命令查询网络端口占用情况

### netstat命令

```shell
netstat -an | grep 3306
```
3306替换成需要grep的端口号

 

### lsof命令

通过list open file命令可以查看到当前打开文件，在linux中所有事物都是以文件形式存在，包括网络连接及硬件设备。

```shell
lsof -i:80
```

-i参数表示网络链接，:80指明端口号，该命令会同时列出PID，方便kill

### 查看所有进程监听的端口

```shell
sudo lsof -i -P | grep -i "listen"
```
