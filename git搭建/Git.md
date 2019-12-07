# Git

## 创建账号

* useradd  名字

```python
ls / 根目录
ls /home/
cat ~/.ssh/config   # cat查看文件
ssh 名字@主机(ip)
# 切换用户
su - 名称
```

* passwd 密码

```python
vi /etc/ssh/
# 修改
vi /etc/ssh/sshd_config
RSAAuthentication  yes
PubkeyAuthentication  yes
PasswordAuthentication  yes
# 需要重启
systemctl restart sshd.service
```

* 生成公钥私钥

```python
ssh-keygen
(默认是id_rsa)./密码名称 
mkdir .ssh   #创建
ls -a  # 打印隐藏文件
# 创建管理公钥文件
touch .ssh/authorized_keys  
# 设置目录权限
chmod 700 .ssh
# 设置文件权限
chmod 600 .ssh/authorized_keys
# 打开
vi .ssh/authorized_keys
# 转到本地 
cat 名称.pub  # 查看公钥
```

* 登录

```python
ssh 名字@ip地址 -i ./密钥
# 使用证书登录   
ssh test_user@62.234.92.111 -i 证书路径
```

## 搭建git服务

1. 创建git用户    useradd git
2. 设置git用户ssh权限目录和文件
3. 创建一个裸版本库

```
su - git                            # 登录git用户
git init --bare project.git         # 创建一个裸仓库
```

## 创建一个文件并提交

```
touch readme.txt                        # 创建readme.txt 文件
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

