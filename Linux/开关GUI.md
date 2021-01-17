# Ubuntu开关GUI

有时为了使用GUI或者完整内核，WSL2无法满足需求，就需要用虚拟机。当虚拟机不需要GUI的时候可以关闭来减少内存消耗

```shell
sudo systemctl set-default multi-user.target # 关闭GUI
sudo systemctl set-default graphical.target # 开启GUI
```

执行完每一条命令都需要reboot。