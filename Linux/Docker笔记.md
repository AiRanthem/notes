# Docker基础

使用Docker中觉得需要记录的部分。随时补充

## 安装

```zsh
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
# 将当前用户添加到docker组才能使用
sudo gpasswd -a ${USER} docker
# 配置完用户组之后要注销一下或者切换一下用户才能生效
```

## 基础操作

按照官方的教程学习。假设现在已经有一个 node.js的web项目叫app。

1. 在项目根目录`/app`创建`Dockerfile`

2. `Dockerfile`内容

   ```dockerfile
   FROM node:12-alpine                # 基础镜像，没有则下载
   WORKDIR /app                       # 指定镜像中工作目录
   COPY . .                           # 将当前代码复制到工作目录中
   RUN yarn install --production      # 创建时执行的指令
   CMD ["node", "/app/src/index.js"]  # 运行容器时自动执行的命令
   ```

3. 在`/app`目录中通过`Dockerfile`创建镜像

   ```zsh
   docker build -t getting-started .
   # -t 命名
   ```

4. 运行容器

   ```zsh
   docker run [CMD] name
   ```

   

5. 一些 run 命令

   + -d 后台运行
   + -p outport:inport 端口映射
   + -it 交互模式（CTRL PQ可以退出）

## 容器管理

```zsh
docker ps # 查看运行中容器
docker ps -a # 查看所有容器，包括停止的
docker images # 查看本地所有镜像
docker stop [NAME]/[CONTAINER ID] # 将容器退出。
docker kill [NAME]/[CONTAINER ID] # 强制停止一个容器
docker start [NAME]/[CONTAINER ID] # 重新启动一个容器
docker attach [NAME]/[CONTAINER ID] # 进入一个容器
docker rm [NAME]/[CONTAINER ID] # 删除一个关闭的容器
docker rm $(docker ps -a -q) # 删除所有容器
docker rmi [IMAGE ID] # 删除一个镜像
docker run [IMAGE NAME] # 创建并启动容器
docker create [IMAGE NAME] # 创建容器，容器处于停止状态

docker commit [CONTAINER ID] [IMAGE NAME] # 把容器打包成镜像
```

## Docker Hub

```zsh
docker login -u YOUR-USER-NAME # 登陆
docker tag [IMAGE NAME] YOUR-USER-NAME/getting-started # 加tag
docker push YOUR-USER-NAME/[IMAGE NAME] # push
```

## 持久化

|                                              | Named Volumes             | Bind Mounts                   |
| :------------------------------------------- | :------------------------ | ----------------------------- |
| Host Location                                | Docker chooses            | You control                   |
| Mount Example (using `-v`)                   | my-volume:/usr/local/data | /path/to/data:/usr/local/data |
| Populates new volume with container contents | Yes                       | No                            |
| Supports Volume Drivers                      | Yes                       | No                            |

### named volume

创建一个命名然后挂载到容器，docker自己会维护这个命名（相当于一个目录）

```zsh
docker volume create <name> # 创建一个命名
docker run -dp 3000:3000 -v <name>:<path-in-docker> getting-started # 挂载
docker volume inspect todo-db # 查看挂载点目录
# Windows系统的挂载点在wsl2里面
```

### Bind Mounts

```zsh
docker run -dp 3000:3000 \
    -w /app -v ${PWD}:/app \ # workplace and volume
    node:12-alpine \ # 镜像
    sh -c "yarn install && yarn run dev" # 运行的命令
```

## 性能监控

```zsh
docker stats
```

# 小技巧

## Docker的存储位置

镜像和容器默认存在 `/var/lib/docker` root分区大小不够的话要修改存储位置

通过软连接实现

```shell
systemctl stop docker
mv /var/lib/docker ~/data/docker
ln -s ~/data/docker /var/lib/docker
systemctl start docker
```

## 国内镜像

```shell
vi /etc/docker/daemon.json
{
    "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
sudo systemctl daemon-reload
sudo systemctl restart docker.service
```

## 使用GUI

```shell
xhost + 
# 注意：使用host模式后，不再能使用-p的端口映射，请注意避开本地端口占用。
docker run -ti --net host -e DISPLAY=$DISPLAY <image>
```

