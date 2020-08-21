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

### chown

改变文件所有者

```bash
chown [-cfhvR] [--help] [--version] user[:group] file...
```

**参数** :

- user : 新的文件拥有者的使用者 ID
- group : 新的文件拥有者的使用者组(group)
- -c : 显示更改的部分的信息
- -f : 忽略错误信息
- -h :修复符号链接
- -v : 显示详细的处理信息
- -R : 处理指定目录以及其子目录下的所有文件
- --help : 显示辅助说明
- --version : 显示版本

## 链接

给文件创建软链接，为log2013.log文件创建软链接link2013，如果log2013.log丢失，link2013将失效

```zsh
ln -s log2013.log link2013
```

给文件创建硬链接，为log2013.log创建硬链接ln2013，log2013.log与ln2013的各项属性相同

```zsh
ln log2013.log ln2013
```

