1. 安装erlang

   ```
   yum install erlang
   ```

   2.下载rpm包

```
 wget http://www.rabbitmq.com/releases/rabbitmq-server/v3.6.15/rabbitmq-server-3.6.15-1.el7.noarch.rpm
```

   3.下载完成后安装

```
yum install rabbitmq-server-3.6.15-1.el7.noarch.rpm
```

   4.安装完后重启服务

```
service rabbitmq-server start
```

   5.查看服务状态

```
service rabbitmq-server status
```

6. 安装插件

```
/sbin/rabbitmq-plugins enable rabbitmq_management 
```

  7.重启服务

```
service rabbitmq-server restart
```

  8.如果找不到配置文件可自行在网上复制一份在/etc/rabbitmq/下创建rabbitmq.config

  9.从3.3.0版本开始，禁止使用guest/guest登录localhost之外的访问。解决办法是，找到配置文件中的：

{loopback_users, [<<"guest">>]},

​        修改为：

{loopback_users, []},

  10.创建账号

```
rabbitmqctl add_user test 123456
```

   11.设置用户角色

```
rabbitmqctl  set_user_tags  test  administrator
```

   12.设置用户权限

```
rabbitmqctl set_permissions -p "/" test ".*" ".*" ".*"
```

   13.设置完成后可以查看当前用户和角色(需要开启服务)

```
rabbitmqctl list_users
```

   14.阿里云安全组配置5672/15672外网可访问