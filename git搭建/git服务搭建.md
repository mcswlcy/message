# 创建用户账号



添加用户： useradd  test_user

设置密码： passwd test_user 

登录： ssh test_user@62.234.92.111          命令格式：     ssh    用户名@ip地址

# 使用公钥私钥登录



## 配置sshd服务参数

```
vi /etc/ssh/sshd_config
RSAAuthentication yes   # 启用RSA 非对称加密算法
PubkeyAuthentication yes   # 公钥认证
PasswordAuthentication yes  #允许密码认证
```

修改配置后需要重启服务：  systemctl restart sshd.service



## 创建ssh权限目录和文件

生成公钥私钥： ssh-keygen

切换用户   su - test_user

创建文件夹：  mkdir .ssh   

设置.ssh目录权限   chmod 700 .ssh

创建文件： touch .ssh/authorized_keys

设置authorized_keys权限   chmod 600 .ssh/authorized_keys

使用证书登录   ssh test_user@62.234.92.111   -i 证书路径



# 搭建git服务

创建git用户    useradd git

设置git用户ssh权限目录和文件

创建一个裸版本库

```
su - git                            # 登录git用户
git init --bare project.git         # 创建一个裸仓库
```



## 创建一个文件并提交

```
touch readme.txt                          # 创建readme.txt 文件
git add readme.txt                         # 添加到暂存区中 
git commit -m "添加readme文件"              # 提交到本地版本库里
git push origin                            # 推送到远程仓库
git pull origin                           # 拉取文件
```



## git 常用命令

```
git status
git 
```









