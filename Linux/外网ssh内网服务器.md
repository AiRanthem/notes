# 外网SSH内网服务器

需求：从外部电脑访问学校的内网GPU服务器

材料：云服务器（做跳板），内网服务器

## 内网服务器操作

```zsh
ssh -NfR 1111:localhost:22 airan@airanthem.cn -p 22 # 端口映射
```

## 云服务器操作

```zsh
# 首先完成内网的端口映射
ssh user@localhost -p1111 # 这条命令可以在云服务器上连接到内网服务器
# 如果要外网进行访问，则通过
ssh -fCNL *:1122:localhost:1111 localhost
```

## 外网访问

```zsh
ssh user@airanthem.cn -p1122
```

## 参考资料

https://www.cnblogs.com/kwongtai/p/6903420.html

https://blog.csdn.net/uisoul/article/details/97416567

