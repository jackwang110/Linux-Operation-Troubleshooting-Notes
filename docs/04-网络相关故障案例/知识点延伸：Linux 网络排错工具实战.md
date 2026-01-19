# 知识点延伸：Linux 网络排错工具实战

## 1. 网络排错基础

### 1.1 网络故障类型

**连接性故障**：
- 无法建立网络连接
- 网络连接中断
- 网络连接不稳定

**性能故障**：
- 网络延迟高
- 网络丢包严重
- 网络带宽不足

**配置故障**：
- IP 地址配置错误
- 网关配置错误
- DNS 配置错误
- 防火墙规则错误

**硬件故障**：
- 网卡故障
- 网络线缆故障
- 交换机/路由器故障

### 1.2 网络排错步骤

1. **确认故障现象**：详细描述网络故障的表现
2. **隔离故障范围**：确定故障影响的范围
3. **收集信息**：使用网络工具收集网络状态信息
4. **分析原因**：基于收集的信息分析故障原因
5. **验证假设**：通过测试验证故障原因的假设
6. **实施解决方案**：根据故障原因实施解决方案
7. **验证解决方案**：测试解决方案是否有效
8. **记录和总结**：记录故障排查过程和解决方案

## 2. 常用网络排错工具

### 2.1 网络连通性测试工具

**ping**：
```bash
# 测试网络连通性
$ ping <ip-address>

# 测试网络连通性（指定包大小）
$ ping -s 1000 <ip-address>

# 测试网络连通性（指定次数）
$ ping -c 10 <ip-address>

# 测试网络连通性（指定间隔）
$ ping -i 0.5 <ip-address>

# 测试网络连通性（使用 IPv6）
$ ping6 <ipv6-address>
```

**mtr**（My TraceRoute）：
```bash
# 测试网络路由和丢包率
$ mtr <ip-address>

# 测试网络路由和丢包率（以报告模式输出）
$ mtr -r <ip-address>

# 测试网络路由和丢包率（指定报告次数）
$ mtr -r -c 10 <ip-address>

# 测试网络路由和丢包率（使用 TCP）
$ mtr --tcp <ip-address>
```

**traceroute**：
```bash
# 测试网络路由路径
$ traceroute <ip-address>

# 测试网络路由路径（使用 ICMP）
$ traceroute -I <ip-address>

# 测试网络路由路径（使用 TCP）
$ traceroute -T <ip-address>

# 测试网络路由路径（指定端口）
$ traceroute -T -p 80 <ip-address>

# 测试网络路由路径（使用 IPv6）
$ traceroute6 <ipv6-address>
```

**tracepath**：
```bash
# 测试网络路由路径
$ tracepath <ip-address>

# 测试网络路由路径（指定最大跳数）
$ tracepath -m 30 <ip-address>

# 测试网络路由路径（使用 IPv6）
$ tracepath6 <ipv6-address>
```

### 2.2 网络配置检查工具

**ifconfig**：
```bash
# 查看所有网络接口状态
$ ifconfig

# 查看特定网络接口状态
$ ifconfig eth0

# 配置网络接口 IP 地址
$ ifconfig eth0 192.168.1.100 netmask 255.255.255.0

# 启用网络接口
$ ifconfig eth0 up

# 禁用网络接口
$ ifconfig eth0 down
```

**ip**：
```bash
# 查看所有网络接口状态
$ ip addr

# 查看特定网络接口状态
$ ip addr show eth0

# 配置网络接口 IP 地址
$ ip addr add 192.168.1.100/24 dev eth0

# 删除网络接口 IP 地址
$ ip addr del 192.168.1.100/24 dev eth0

# 启用网络接口
$ ip link set eth0 up

# 禁用网络接口
$ ip link set eth0 down
```

**route**：
```bash
# 查看路由表
$ route -n

# 添加默认网关
$ route add default gw 192.168.1.1

# 删除默认网关
$ route del default gw 192.168.1.1

# 添加静态路由
$ route add -net 10.0.0.0/8 gw 192.168.1.2

# 删除静态路由
$ route del -net 10.0.0.0/8 gw 192.168.1.2
```

**ip route**：
```bash
# 查看路由表
$ ip route

# 添加默认网关
$ ip route add default via 192.168.1.1 dev eth0

# 删除默认网关
$ ip route del default via 192.168.1.1 dev eth0

# 添加静态路由
$ ip route add 10.0.0.0/8 via 192.168.1.2 dev eth0

# 删除静态路由
$ ip route del 10.0.0.0/8 via 192.168.1.2 dev eth0

# 查看路由缓存
$ ip route show cache
```

### 2.3 端口和连接检查工具

**netstat**：
```bash
# 查看所有网络连接
$ netstat -a

# 查看 TCP 网络连接
$ netstat -t

# 查看 UDP 网络连接
$ netstat -u

# 查看监听端口
$ netstat -l

# 查看网络连接的进程信息
$ netstat -p

# 查看网络连接的详细信息
$ netstat -n

# 组合使用
$ netstat -tuln
$ netstat -antp
```

**ss**（Socket Statistics）：
```bash
# 查看所有网络连接
$ ss -a

# 查看 TCP 网络连接
$ ss -t

# 查看 UDP 网络连接
$ ss -u

# 查看监听端口
$ ss -l

# 查看网络连接的进程信息
$ ss -p

# 查看网络连接的详细信息
$ ss -n

# 组合使用
$ ss -tuln
$ ss -antp

# 查看特定端口的连接
$ ss -tuln | grep <port>
```

**lsof**（List Open Files）：
```bash
# 查看所有打开的网络文件
$ lsof -i

# 查看特定端口的打开文件
$ lsof -i :80

# 查看特定进程打开的网络文件
$ lsof -i -p <pid>

# 查看特定用户打开的网络文件
$ lsof -i -u <user>

# 查看 TCP 连接
$ lsof -i tcp

# 查看 UDP 连接
$ lsof -i udp
```

### 2.4 网络性能测试工具

**iperf3**：
```bash
# 作为服务器运行
$ iperf3 -s

# 作为客户端连接服务器（测试 TCP 带宽）
$ iperf3 -c <server-ip>

# 作为客户端连接服务器（测试 UDP 带宽）
$ iperf3 -c <server-ip> -u

# 作为客户端连接服务器（指定带宽）
$ iperf3 -c <server-ip> -b 1G

# 作为客户端连接服务器（指定时间）
$ iperf3 -c <server-ip> -t 60

# 作为客户端连接服务器（指定并行连接数）
$ iperf3 -c <server-ip> -P 10
```

**nuttcp**：
```bash
# 作为服务器运行
$ nuttcp -S

# 作为客户端连接服务器（测试 TCP 带宽）
$ nuttcp <server-ip>

# 作为客户端连接服务器（测试 UDP 带宽）
$ nuttcp -u <server-ip>

# 作为客户端连接服务器（指定带宽）
$ nuttcp -b 100M <server-ip>

# 作为客户端连接服务器（指定时间）
$ nuttcp -t 60 <server-ip>
```

**ping**（用于测试延迟）：
```bash
# 测试网络延迟
$ ping -c 10 <ip-address>

# 测试网络延迟（更精确）
$ ping -c 10 -i 0.2 <ip-address>
```

### 2.5 DNS 测试工具

**nslookup**：
```bash
# 测试 DNS 解析
$ nslookup <domain>

# 测试 DNS 解析（使用指定 DNS 服务器）
$ nslookup <domain> <dns-server>

# 测试反向 DNS 解析
$ nslookup <ip-address>
```

**dig**（Domain Information Groper）：
```bash
# 测试 DNS 解析
$ dig <domain>

# 测试 DNS 解析（简洁输出）
$ dig +short <domain>

# 测试 DNS 解析（使用指定 DNS 服务器）
$ dig @<dns-server> <domain>

# 测试反向 DNS 解析
$ dig -x <ip-address>

# 测试 DNS 解析（指定记录类型）
$ dig <domain> A
$ dig <domain> MX
$ dig <domain> TXT
```

**host**：
```bash
# 测试 DNS 解析
$ host <domain>

# 测试 DNS 解析（使用指定 DNS 服务器）
$ host <domain> <dns-server>

# 测试反向 DNS 解析
$ host <ip-address>
```

### 2.6 网络诊断工具

**ethtool**：
```bash
# 查看网络接口信息
$ ethtool <interface>

# 查看网络接口驱动信息
$ ethtool -i <interface>

# 查看网络接口统计信息
$ ethtool -S <interface>

# 测试网络接口故障
$ ethtool -t <interface> online

# 修改网络接口速度
$ ethtool -s <interface> speed 1000 duplex full autoneg off
```

**tcpdump**：
```bash
# 捕获网络数据包
$ tcpdump -i <interface>

# 捕获网络数据包（保存到文件）
$ tcpdump -i <interface> -w <file>

# 捕获网络数据包（从文件读取）
$ tcpdump -r <file>

# 捕获网络数据包（过滤 IP）
$ tcpdump -i <interface> host <ip-address>

# 捕获网络数据包（过滤端口）
$ tcpdump -i <interface> port <port>

# 捕获网络数据包（过滤协议）
$ tcpdump -i <interface> tcp

# 捕获网络数据包（详细输出）
$ tcpdump -i <interface> -v

# 捕获网络数据包（更详细输出）
$ tcpdump -i <interface> -vv

# 捕获网络数据包（显示数据包内容）
$ tcpdump -i <interface> -A
```

**wireshark**：
```bash
# 图形化网络数据包分析工具
# 需要安装：apt install wireshark 或 yum install wireshark

# 启动 wireshark
$ wireshark

# 捕获网络数据包（命令行）
$ tshark -i <interface>

# 捕获网络数据包（保存到文件）
$ tshark -i <interface> -w <file>

# 捕获网络数据包（过滤）
$ tshark -i <interface> -f "host <ip-address>"
```

### 2.7 网络配置工具

**ifup/ifdown**：
```bash
# 启用网络接口
$ ifup <interface>

# 禁用网络接口
$ ifdown <interface>
```

**nmcli**（NetworkManager Command Line Interface）：
```bash
# 查看网络连接
$ nmcli connection show

# 查看网络设备
$ nmcli device show

# 启用网络连接
$ nmcli connection up <connection-name>

# 禁用网络连接
$ nmcli connection down <connection-name>

# 添加网络连接
$ nmcli connection add con-name <name> ifname <interface> type ethernet ip4 <ip-address>/24 gw4 <gateway>

# 修改网络连接
$ nmcli connection modify <connection-name> ipv4.addresses <ip-address>/24
```

**networkctl**（systemd-networkd Command Line Interface）：
```bash
# 查看网络状态
$ networkctl status

# 查看网络接口状态
$ networkctl status <interface>

# 重新加载网络配置
$ networkctl reload

# 重启网络接口
$ networkctl restart <interface>
```

## 3. 网络故障排查实战

### 3.1 服务器无法访问外网

**故障现象**：
- 服务器无法 ping 通外部 IP 地址
- 服务器无法解析外部域名
- 内部网络通信正常

**排查步骤**：
1. **检查网络接口状态**：`ifconfig` 或 `ip addr`
2. **检查路由表**：`route -n` 或 `ip route`
3. **测试网关连通性**：`ping <gateway-ip>`
4. **测试外部 IP 连通性**：`ping 8.8.8.8`
5. **测试 DNS 解析**：`nslookup google.com`
6. **检查防火墙规则**：`iptables -L -n`
7. **查看系统日志**：`tail -n 100 /var/log/syslog`

**常见原因**：
- 网关配置错误
- DNS 配置错误
- 防火墙规则阻止
- 网络接口配置错误

### 3.2 端口无法访问

**故障现象**：
- 外部无法访问服务器的特定端口
- 内部可以访问该端口
- 服务运行正常

**排查步骤**：
1. **检查服务状态**：`systemctl status <service-name>`
2. **检查端口监听状态**：`netstat -tuln` 或 `ss -tuln`
3. **测试内部端口访问**：`telnet localhost <port>`
4. **测试外部端口访问**：`telnet <server-ip> <port>`
5. **检查防火墙规则**：`iptables -L -n`
6. **检查网络连接状态**：`netstat -ant` 或 `ss -ant`
7. **查看应用程序日志**：`tail -n 100 /var/log/<service-name>.log`

**常见原因**：
- 防火墙规则阻止
- 服务未监听预期端口
- 服务未绑定到正确的网络接口
- 端口被占用

### 3.3 网络丢包严重

**故障现象**：
- `ping` 命令显示丢包率高
- 网络连接不稳定
- 应用程序响应缓慢

**排查步骤**：
1. **测试网络连通性和丢包率**：`ping -c 100 <ip-address>`
2. **测试网络路由和丢包率**：`mtr <ip-address>`
3. **检查网络接口状态**：`ifconfig` 或 `ip addr`
4. **检查网络接口错误计数**：`ethtool -S <interface>`
5. **检查网络流量**：`sar -n DEV 1`
6. **检查路由表**：`route -n` 或 `ip route`
7. **检查网络设备状态**：查看交换机、路由器状态

**常见原因**：
- 网卡故障
- 网络线缆故障
- 交换机/路由器故障
- 网络拥塞
- 路由问题

### 3.4 DNS 解析失败

**故障现象**：
- 无法解析域名
- 可以 ping 通 IP 地址，但无法访问域名
- 应用程序无法通过域名访问服务

**排查步骤**：
1. **测试 DNS 解析**：`nslookup <domain>`
2. **测试 DNS 解析（使用指定 DNS 服务器）**：`nslookup <domain> 8.8.8.8`
3. **检查 DNS 配置**：`cat /etc/resolv.conf`
4. **检查 hosts 文件**：`cat /etc/hosts`
5. **测试 DNS 服务器连通性**：`ping <dns-server-ip>`
6. **检查防火墙规则**：`iptables -L -n`
7. **查看系统日志**：`tail -n 100 /var/log/syslog`

**常见原因**：
- DNS 配置错误
- DNS 服务器故障
- 防火墙规则阻止 DNS 查询
- hosts 文件配置错误

## 4. 网络排错工具的高级使用

### 4.1 网络数据包分析

**tcpdump 高级过滤**：
```bash
# 过滤特定 IP 的数据包
$ tcpdump -i eth0 host <ip-address>

# 过滤特定端口的数据包
$ tcpdump -i eth0 port <port>

# 过滤特定协议的数据包
$ tcpdump -i eth0 tcp

# 过滤特定 MAC 地址的数据包
$ tcpdump -i eth0 ether host <mac-address>

# 组合过滤条件
$ tcpdump -i eth0 host <ip-address> and port <port>
$ tcpdump -i eth0 host <ip-address> or host <another-ip>
$ tcpdump -i eth0 not host <ip-address>

# 过滤特定标志的 TCP 数据包
$ tcpdump -i eth0 tcp[tcpflags] & tcp-syn != 0
$ tcpdump -i eth0 tcp[tcpflags] & tcp-ack != 0
$ tcpdump -i eth0 tcp[tcpflags] & tcp-rst != 0
```

**wireshark 高级分析**：
- 使用过滤器过滤数据包
- 使用统计功能分析网络流量
- 使用专家信息分析网络问题
- 使用追踪流功能分析 TCP 会话

### 4.2 网络性能分析

**iperf3 高级测试**：
```bash
# 测试网络带宽（双向）
$ iperf3 -c <server-ip> -d

# 测试网络带宽（指定窗口大小）
$ iperf3 -c <server-ip> -w 1M

# 测试网络带宽（指定 MSS）
$ iperf3 -c <server-ip> -M 1460

# 测试网络带宽（使用 IPv6）
$ iperf3 -c <ipv6-address>
```

**网络流量分析**：
```bash
# 查看网络流量
$ sar -n DEV 1

# 查看网络协议统计
$ netstat -s

# 查看网络连接状态
$ netstat -ant

# 查看网络连接的进程信息
$ netstat -antp
```

### 4.3 网络安全分析

**端口扫描**：
```bash
# 使用 nmap 进行端口扫描
# 需要安装：apt install nmap 或 yum install nmap

# 扫描开放端口
$ nmap <ip-address>

# 扫描开放端口（详细）
$ nmap -v <ip-address>

# 扫描开放端口（指定端口范围）
$ nmap -p 1-1000 <ip-address>

# 扫描开放端口（使用 TCP SYN 扫描）
$ nmap -sS <ip-address>

# 扫描开放端口（使用 UDP 扫描）
$ nmap -sU <ip-address>

# 扫描开放端口（操作系统检测）
$ nmap -O <ip-address>
```

**网络连接分析**：
```bash
# 查看网络连接状态
$ netstat -ant

# 查看网络连接的进程信息
$ netstat -antp

# 查看可疑网络连接
$ netstat -antp | grep ESTABLISHED

# 查看网络连接的详细信息
$ ss -antp
```

## 5. 网络排错最佳实践

### 5.1 工具选择

- **网络连通性**：ping, mtr, traceroute
- **端口和连接**：netstat, ss, lsof
- **网络配置**：ifconfig, ip, route, nmcli
- **网络性能**：iperf3, ping, sar
- **DNS 问题**：nslookup, dig, host
- **数据包分析**：tcpdump, wireshark
- **网络接口**：ethtool, ifconfig, ip

### 5.2 排查技巧

1. **从底层到上层**：先检查物理连接，再检查网络配置，最后检查应用程序
2. **从局部到整体**：先测试本地连接，再测试内部网络，最后测试外部网络
3. **对比测试**：与正常状态对比，找出差异
4. **逐步排除**：通过测试逐步排除可能的故障原因
5. **记录和分析**：详细记录排查过程和结果，便于分析和总结
6. **使用多种工具**：结合使用多种工具，获取更全面的信息
7. **保持冷静**：网络故障可能很复杂，保持冷静和系统性思维

### 5.3 预防措施

1. **定期检查**：定期检查网络设备状态和配置
2. **监控系统**：部署网络监控系统，及时发现网络问题
3. **备份配置**：定期备份网络设备配置
4. **文档管理**：建立网络拓扑和配置文档
5. **培训和演练**：定期进行网络故障排查培训和演练
6. **冗余设计**：设计网络冗余，提高网络可靠性
7. **安全措施**：实施网络安全措施，防止网络攻击

## 6. 总结

网络排错是系统运维中的重要技能，需要掌握多种网络工具和排查方法。通过本文介绍的网络排错工具和实战案例，希望读者能够更好地理解和应用这些工具，提高网络故障排查的效率和准确性。

在实际网络排错中，需要根据具体的故障现象选择合适的工具和方法，结合系统性思维和经验判断，逐步分析和解决网络问题。同时，通过定期的网络维护和监控，可以预防和减少网络故障的发生，提高网络的可靠性和稳定性。