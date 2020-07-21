# FRP实现内网穿透

https://sspai.com/post/52523

## 需要

公网服务器: 服务端

内网服务器: 客户端

两台服务器都要先下载解压.

```zsh
# 下载解压
wget https://github.com/fatedier/frp/releases/download/v0.22.0/frp_0.22.0_linux_amd64.tar.gz
tar -zxvf frp_0.22.0_linux_amd64.tar.gz
```

## 服务端设置

配置文件 `frps.isi`

```ini
[common]
bind_port = 7000
dashboard_port = 7500
token = 12345678
dashboard_user = admin
dashboard_pwd = admin
vhost_http_port = 10080
vhost_https_port = 10443
```

- “bind_port”表示用于客户端和服务端连接的端口，这个端口号我们之后在配置客户端的时候要用到。
- “dashboard_port”是服务端仪表板的端口，若使用7500端口，在配置完成服务启动后可以通过浏览器访问 x.x.x.x:7500 （其中x.x.x.x为VPS的IP）查看frp服务运行信息。
- “token”是用于客户端和服务端连接的口令，请自行设置并记录，稍后会用到。
- “dashboard_user”和“dashboard_pwd”表示打开仪表板页面登录的用户名和密码，自行设置即可。
- “vhost_http_port”和“vhost_https_port”用于反向代理HTTP主机时使用，本文不涉及HTTP协议，因而照抄或者删除这两条均可。

运行服务器

```zsh
nohup ./frps -c frps.ini &
```

## 客户端设置

配置文件`frpc.ini`

```ini
[common]
server_addr = x.x.x.x
server_port = 7000
token = won517574356
[rdp]
type = tcp
local_ip = 127.0.0.1           
local_port = 3389
remote_port = 7001  
[smb]
type = tcp
local_ip = 127.0.0.1
local_port = 445
remote_port = 7002
```

其中common字段下的三项即为服务端的设置。

- “server_addr”为服务端IP地址，填入即可。
- “server_port”为服务器端口，填入你设置的端口号即可，如果未改变就是7000
- “token”是你在服务器上设置的连接口令，原样填入即可。

运行客户端

```zsh
nohup ./frpc -c frpc.ini &
```

