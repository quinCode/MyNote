保证虚拟机IP地址不变，

使物理机或SecureCRT/Xshell终端软件可以访问

1. 设置静态IP

   **vi /etc/sysconfig/network-scripts/ifcfg-eth0**

   注意查看网卡名 如eth0

   如设置服务器IP为192.168.137.200

   ```typescript
   BOOTPROTO=static
   DEVICE=eth0
   ONBOOT=yes
   IPADDR=192.168.137.200
   GATEWAY=192.168.137.1
   DNS1=192.168.137.1
   NETMASK=255.255.255.0
   ```

   systemctl restart network

2. Hyper-V 虚拟交换机新建内部网络

3. 物理机网络设置

   设置IP地址和子网掩码

4. 物理机网络连接共享设置

