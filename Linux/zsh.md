# zsh & oh my zsh

## 查看系统shell

```zsh
cat /etc/shells
```

## 安装zsh

```zsh
yum install zsh
apt install zsh
```

## 设置默认shell

```zsh
chsh -s /bin/zsh

# Mac如下
# 在 /etc/shells 文件中加入如下一行
/usr/local/bin/zsh
# 接着运行
chsh -s /usr/local/bin/zsh
```

## 安装oh-my-zsh

```zsh
# 自动
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
# 手动
git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```

## 插件

```zsh
# ~/.zshrc plugins关键字
# 自带插件
extract z wd

# 字体高亮
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting


```

