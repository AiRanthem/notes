# 通过docker安装Anaconda镜像

## 搜索镜像

```zsh
docker search anaconda
```

## 拉取镜像

```zsh
docker pull continuumio/anaconda3
```

## 运行镜像, 指定网络端口

```zsh
docker run -i -t -p 8888:8888 continuumio/anaconda3 /bin/bash
#-i: 以交互模式运行容器，通常与 -t 同时使用；
#-t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用；
#-p: 指定端口映射，格式为：主机(宿主)端口:容器端口
```

## 进出容器

`ctrl + P + Q` 退出容器但是不关闭

```zsh
# 回到容器
docker ps # 查看运行中的容器,第一列获取CONTAINER ID
docker attach <CONTAINER ID>
```

## 其他

```zsh
# 运行jupyter notebook
jupyter notebook --ip 0.0.0.0 --no-browser --allow-root
```

## 容器允许远程登陆

```zsh
# 安装openssh-server
apt update && apt install -y --no-install-recommends openssh-server

# 允许root登陆
nano /etc/ssh/sshd_config
#######
RSAAuthentication yes #启用 RSA 认证
PubkeyAuthentication yes #启用公钥私钥配对认证方式
AuthorizedKeysFile .ssh/authorized_keys #公钥文件路径（和上面生成的文件同）
PermitRootLogin yes #root能使用ssh登录
#######

# 启动ssh
service ssh start

# 客户端连接
ssh root@your-server-ip -A -p your-port
```



