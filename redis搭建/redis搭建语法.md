# 搭建redis服务

### Redis安装

```python
下载: wget http://download.redis.io/releases/redis-5.0.5.tar.gz
解包: tar xzf redis-5.0.5.tar.gz
      cd redis-5.0.5
make (编译)
redis配置需要修改内容:
    注释掉:bind 127.0.0.1
    requirepass 'abc'
    daemonize no 改为  daemonize yes
    protected-mode yes改为protected-mode no
运行服务: ./src/redis-server  redis.conf
```

- 添加防火墙

```pytoh
1 确认防火墙是否开启了6379端口

添加端口：firewall-cmd --zone=public --add-port=6379/tcp --permanent

刷新防火墙：firewall-cmd --reload

2 如果你是阿里云的服务器，那么可能还需要在安全组中开启端口
```



| -安全组规则,授权策略 | -协议范围 | -端口范围 | -授权范围      | 授权对象  | 描述  | 优先级 | 创建时间  |
| -------------------- | --------- | --------- | -------------- | --------- | ----- | ------ | --------- |
| 允许                 | 自定义TCP | 6379/6379 | IPv4地址段访问 | 0.0.0.0/0 | redis | 30     | 2019-9-23 |



* 基本命令

```python
# pwd       显示当前位置
# ls        显示所有信息
# mkdir     创建文件夹
# ls -a     显示全部文件(包括隐藏信息)
# ls -l     显示权限(ls -l显示文件属性(1.用户名,2.主机名称),(1.文件所有者,2.组的概念--有些权限都能看见)
				[以-开头的是文件,以d开头的是文件夹,rwx可读可写可执行],[r4,w2,x1]为7,说明三者权限都有)
# touch a.txt    创建一个文件(默认没有执行的权限)
# chmod 700(00代表消除权限)a.txt
# mv a.txt mydir     把一个文件移动到另一个文件夹
# man ls     查看一个文档帮助(format格式[--代表全称,-代表缩写])

# vi Readme.md   每个下载包都会自带的文档
# vi ~/.ssh.
# id_rsa     私钥

# ps aux      列举所有的进程,查看进程是否启动
# ps aux | grep redis    |管道符     grep筛选    意思就是筛选出redis的所有进程
# kill -9 11243     *11243 进程的id*            杀死进程(俩种(1. -15需告知要把你杀死了,2. -9强制杀死))




# 启动redis服务
src/redis-server
cat ~/.ssh/config
redis-conf    服务器redis配置文件(bind 127.0.0.1 注释掉)
vi 里  /   搜索
requirepass 'abc'    设置连接密码
src/redis-server redis.conf      再次启动服务需要跟一个配置文件

# reids客户端
src/reids-cli  [没有参数的就是本机连接127.0.0.1]

redis-cli --help
jsage:  用法

redis-cli -h 地址
redis-cli -h 地址 -a 密码
    
    
    
```

















