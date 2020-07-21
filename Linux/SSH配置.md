# SSH

## SSH超时断开

```zsh
# 服务器端配置
# /etc/ssh/sshd_config
ClientAliveInterval  60
ClientAliveCountMax  60

# 客户端配置
# /etc/ssh/ssh_config
ServerAliveInterval  60
ServerAliveCountMax  60

# 连接时加参数
ssh -o ServerAliveInterval=30 root@192.168.1.1
```

## 免密码登陆

```zsh
# Client
ssh-keygen -t rsa
scp ~/.ssh/id_rsa.pub user@ip:~/.ssh
# Server
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

## SCP端口选择

```zsh
scp -P port user@ip:/upload/path
```

