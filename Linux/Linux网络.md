# 没有网络解决方案

## 网络适配器没有启动

```shell
2: ens33: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 00:0c:29:95:34:f1 brd ff:ff:ff:ff:ff:ff
    altname enp2s1
```

比如上面这种状态，可以通过 `sudo /sbin/dhclient` 来解决，解决后

```shell
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:95:34:f1 brd ff:ff:ff:ff:ff:ff
    altname enp2s1
    inet 192.168.146.128/24 brd 192.168.146.255 scope global dynamic ens33
       valid_lft 1797sec preferred_lft 1797sec
    inet6 fe80::20c:29ff:fe95:34f1/64 scope link 
       valid_lft forever preferred_lft forever
```

## 网络配置

1. 首先得到网卡名称：`ip addr` or `ls /sys/class/net/`，以下假设网卡名为`eth0`，实际中应替换为自己实际的名称。

2. 设置文件为：

   ```
   /etc/network/interfaces
   ```

   1. 使用DHCP方式，在文件底部添加：

   ```
   auto eth0
   allow-hotplug eth0
   iface eth0 inet dhcp
   ```

   1. 手动设置IP上网，在文件底部添加：

   ```
   auto eth0
   iface eth0 inet static
       address 192.168.0.7
       netmask 255.255.255.0
       gateway 192.168.0.254
   ```

3. 重启网络服务`sudo systemctl restart networking.service`