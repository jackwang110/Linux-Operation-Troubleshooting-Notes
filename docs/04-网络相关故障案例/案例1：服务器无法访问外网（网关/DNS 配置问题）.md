# 案例1：服务器无法访问外网（网关/DNS 配置问题）

## 故障现象

### 1. 告警信息

- 监控系统发出服务器无法访问外网的告警
- 告警级别：严重
- 告警时间：2026-01-25 09:15:00

### 2. 服务状态

- 服务器无法 ping 通外部 IP 地址
- 服务器无法解析外部域名
- 内部网络通信正常
- 可能出现应用程序无法连接外部服务的情况

### 3. 系统表现

- `ping 8.8.8.8` 显示超时
- `nslookup google.com` 显示 DNS 解析失败
- `route -n` 显示路由表异常
- `/etc/resolv.conf` 显示 DNS 配置错误

## 排查步骤

### 1. 检查网络连通性

```bash
# 测试本地回环地址
$ ping 127.0.0.1

# 测试内部网络连通性
$ ping <internal-ip>

# 测试网关连通性
$ ping <gateway-ip>

# 测试外部 IP 连通性
$ ping 8.8.8.8

# 测试外部域名连通性
$ ping google.com
```

### 2. 检查路由表

```bash
# 查看路由表
$ route -n

# 查看默认网关
$ route -n | grep UG

# 检查路由表是否有默认路由
$ ip route

# 检查网络接口状态
$ ip addr
```

### 3. 检查 DNS 配置

```bash
# 查看 DNS 配置
$ cat /etc/resolv.conf

# 测试 DNS 解析
$ nslookup google.com

# 测试指定 DNS 服务器解析
$ nslookup google.com 8.8.8.8

# 检查 DNS 服务状态
$ systemctl status resolvconf
$ systemctl status systemd-resolved
```

### 4. 检查网络接口配置

```bash
# 查看网络接口配置
$ ifconfig
$ ip addr

# 查看网络接口状态
$ ip link

# 查看网络接口详细信息
$ ethtool <interface>

# 检查网络接口驱动
$ ethtool -i <interface>
```

### 5. 检查防火墙规则

```bash
# 查看防火墙规则（iptables）
$ iptables -L -n

# 查看防火墙规则（firewalld）
$ firewall-cmd --list-all

# 临时关闭防火墙测试
$ systemctl stop iptables
$ systemctl stop firewalld
```

### 6. 检查系统日志

```bash
# 查看系统日志中的网络错误
$ dmesg | grep -i "error\|fail\|warn"

# 查看系统日志中的网络相关信息
$ dmesg | grep -i "network\|eth\|wlan"

# 查看系统日志
$ tail -n 100 /var/log/syslog

# 查看网络服务日志
$ journalctl -u networking
$ journalctl -u NetworkManager
```

### 7. 检查网络服务状态

```bash
# 查看网络服务状态
$ systemctl status networking
$ systemctl status NetworkManager

# 重启网络服务
$ systemctl restart networking
$ systemctl restart NetworkManager

# 重新加载网络配置
$ ifdown <interface> && ifup <interface>
```

## 根因分析

### 1. 现象复述

服务器无法访问外网，无法 ping 通外部 IP 地址，无法解析外部域名，但内部网络通信正常。经排查，发现服务器无法访问外网的原因可能是网关配置错误或 DNS 配置问题。

### 2. 根因假设

**假设 A：网关配置错误**
- 现象：`route -n` 显示路由表中没有默认网关，或默认网关配置错误
- 机制解释：服务器没有配置正确的默认网关，导致数据包无法路由到外部网络
- 验证方法：检查路由表，测试网关连通性
- 影响范围：服务器无法访问所有外部网络
- 可能性：高

**假设 B：DNS 配置错误**
- 现象：`cat /etc/resolv.conf` 显示 DNS 服务器配置错误，`nslookup google.com` 解析失败
- 机制解释：服务器配置了错误的 DNS 服务器，导致无法解析外部域名
- 验证方法：检查 DNS 配置，测试 DNS 解析
- 影响范围：服务器无法解析外部域名，但可能可以访问外部 IP 地址
- 可能性：高

**假设 C：防火墙规则阻止**
- 现象：`iptables -L -n` 显示防火墙规则阻止了外部网络访问
- 机制解释：防火墙规则设置不当，阻止了服务器访问外部网络
- 验证方法：检查防火墙规则，临时关闭防火墙测试
- 影响范围：服务器无法访问外部网络，或只能访问特定端口
- 可能性：中

**假设 D：网络接口故障**
- 现象：`ip link` 显示网络接口状态异常，`ethtool <interface>` 显示接口错误
- 机制解释：网络接口硬件故障或驱动问题，导致网络通信异常
- 验证方法：检查网络接口状态，测试接口驱动
- 影响范围：服务器可能无法访问任何网络
- 可能性：中

**假设 E：网络线缆问题**
- 现象：网络接口显示连接，但无法 ping 通网关
- 机制解释：网络线缆松动或损坏，导致物理连接故障
- 验证方法：检查网络线缆连接，更换线缆测试
- 影响范围：服务器无法访问任何网络
- 可能性：低

## 解决方案

### 1. 紧急处理

1. **添加默认网关**
   ```bash
   # 添加默认网关
   $ route add default gw <gateway-ip>
   
   # 或使用 ip 命令
   $ ip route add default via <gateway-ip>
   ```

2. **修正 DNS 配置**
   ```bash
   # 编辑 DNS 配置文件
   $ vi /etc/resolv.conf
   # 添加正确的 DNS 服务器
   nameserver 8.8.8.8
   nameserver 8.8.4.4
   ```

3. **修正防火墙规则**
   ```bash
   # 允许所有出站流量
   $ iptables -A OUTPUT -j ACCEPT
   
   # 或临时关闭防火墙
   $ systemctl stop iptables
   $ systemctl stop firewalld
   ```

4. **重启网络服务**
   ```bash
   # 重启网络服务
   $ systemctl restart networking
   $ systemctl restart NetworkManager
   
   # 重新加载网络配置
   $ ifdown <interface> && ifup <interface>
   ```

### 2. 根本解决

1. **网关配置问题**
   - 在网络接口配置文件中添加正确的默认网关
   - 对于 Debian/Ubuntu：编辑 `/etc/network/interfaces`
   - 对于 CentOS/RHEL：编辑 `/etc/sysconfig/network-scripts/ifcfg-<interface>`
   - 确保网关地址在同一网段内

2. **DNS 配置问题**
   - 在 `/etc/resolv.conf` 中添加正确的 DNS 服务器
   - 对于使用 NetworkManager 的系统，通过 nmcli 配置 DNS
   - 考虑使用本地 DNS 缓存服务，如 dnsmasq
   - 定期检查 DNS 服务器的可用性

3. **防火墙配置问题**
   - 配置正确的防火墙规则，允许必要的出站流量
   - 使用防火墙管理工具，如 firewalld 或 iptables-persistent
   - 定期审查防火墙规则，确保规则的正确性

4. **网络接口问题**
   - 确保网络接口驱动正确安装
   - 定期检查网络接口状态
   - 考虑使用冗余网络接口，提高网络可靠性

### 3. 验证结果

- 服务器可以 ping 通外部 IP 地址
- 服务器可以解析外部域名
- 应用程序可以连接外部服务
- 监控系统不再发出告警

## 预防措施

### 1. 技术措施

1. **网络配置管理**
   - 使用版本控制工具管理网络配置文件
   - 建立网络配置基线，确保配置一致性
   - 定期备份网络配置文件

2. **网络监控**
   - 部署网络监控系统，及时发现网络异常
   - 监控网络连通性，包括内部网络和外部网络
   - 监控 DNS 解析状态，及时发现 DNS 问题

3. **自动化配置**
   - 使用配置管理工具（如 Ansible、Puppet）管理网络配置
   - 实施网络配置的自动化部署
   - 建立配置变更的自动验证机制

4. **冗余设计**
   - 配置多个 DNS 服务器，提高 DNS 解析的可靠性
   - 考虑使用多网关冗余，提高网络连接的可靠性
   - 实施网络接口绑定（bonding），提高网络接口的可靠性

5. **文档管理**
   - 建立网络拓扑图，记录网络设备和连接关系
   - 记录网络配置变更历史，便于问题排查
   - 编写网络故障处理手册，规范故障处理流程

### 2. 流程措施

1. **变更管理**
   - 对网络配置的变更进行审批和测试
   - 实施变更前备份当前配置
   - 建立变更回滚机制

2. **定期检查**
   - 定期检查网络连通性，确保网络正常运行
   - 定期检查 DNS 配置，确保 DNS 解析正常
   - 定期检查防火墙规则，确保规则的正确性

3. **应急响应**
   - 制定网络故障的应急响应流程
   - 建立网络故障的上报和处理机制
   - 定期演练网络故障的处理流程

4. **培训和知识共享**
   - 对运维人员进行网络知识培训
   - 分享网络故障处理的经验和教训
   - 建立网络故障处理的知识库

### 3. 人员措施

1. **技能培训**
   - 对运维人员进行网络基础知识培训
   - 提高运维人员对网络故障的认识和处理能力
   - 培训运维人员使用网络监控和故障排查工具

2. **责任明确**
   - 明确网络管理的责任人和联系方式
   - 建立网络故障的上报和处理流程
   - 确保运维人员了解网络故障的影响范围

3. **团队协作**
   - 加强网络团队和系统团队之间的沟通
   - 建立网络变更的协作机制
   - 定期召开网络状态的评审会议

## 总结

本案例中，服务器无法访问外网的根本原因可能是网关配置错误或 DNS 配置问题。通过系统的排查步骤，我们可以定位具体的问题并采取相应的解决方案。

网络连通性是服务器正常运行的基础，需要通过合理的网络配置、监控和管理来确保网络的可靠性和稳定性。通过建立完善的预防措施，可以减少网络故障的发生，提高系统的可用性。

此案例提醒我们，在遇到网络故障时，需要从多个方面进行排查，找出根本原因并采取相应的解决方案。同时，通过定期的检查和监控，可以预防和减少类似问题的发生。