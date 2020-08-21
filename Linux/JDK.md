# JDK

Ubuntu下配置JDK的方法

## 添加PPA源

```shell
sudo add-apt-repository ppa:linuxuprising/java   (oracle jdk)

sudo add-apt-repository ppa:openjdk-r/ppa   (openJDK)

sudo apt-get update
```

## 安装

以安装jdk14为例。其他版本一样

```shell
sudo apt -y install oracle-java14-installer
# 安装并设为默认
sudo apt -y install oracle-java14-set-default
```

## 切换

其实我觉得装14就够了，不过有可能需要切换

```SHELL
sudo update-java-alternatives -s java-8-oracle
```

