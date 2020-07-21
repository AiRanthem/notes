# nVidia

## 查看GPU型号

```zsh
lspci | grep -i nvidia
```

## 查看NVIDIA驱动版本

```zsh
sudo dpkg --list | grep nvidia-*
nvidia-msi # 这个比较方便
```

## 安装驱动

1. 去[官网](http://www.geforce.cn/drivers)搜索适合的驱动程序

2. 删除之前的驱动`sudo apt-get --purge remove nvidia-*`

3. 通过PPA安装驱动

   ```zsh
   sudo add-apt-repository ppa:graphics-drivers/ppa #安装指定PPA源，执行该指令后会出现大段内容介绍，按[Enter]键执行即可
   sudo apt-get update          #更新源
   sudo apt-get install nvidia-384  #用之前search一下版本
   sudo apt-get install mesa-common-dev  #CUDA所需要的库
   sudo apt-get install freeglut3-de
   ```

## 添加docker支持

需要安装好驱动以及docker19.03，不用装cuda

```zsh
# Add the package repositories
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit
sudo systemctl restart docker
```

