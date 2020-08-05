# Linux命令

## 查找

```zsh
# 查看命令对应的可执行文件位置
which <cmd>
# 寻找对应软件的目录
whereis <app>
```

## 文件操作

```zsh
# 批量重命名
# rename命令有两个版本，可以都试试看确定是哪个版本的
rename "s/oldstr/newstr/" <files> # perl version
rename <oldstr> <newstr> <files>  # C version

```

