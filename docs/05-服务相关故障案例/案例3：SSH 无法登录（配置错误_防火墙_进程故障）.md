# 案例3：SSH 无法登录（配置错误/防火墙/进程故障）

## 现象
- SSH 客户端无法登录
- 连接被拒绝
- 配置错误

## 排查步骤
1. 检查服务状态：`systemctl status sshd`
2. 检查配置：`sshd -t`
3. 检查防火墙：`iptables -L`

## 根因分析
- 服务未启动
- 配置文件错误
- 防火墙阻止

## 解决方案
- 启动服务：`systemctl start sshd`
- 修复配置：修正 `sshd_config`
- 开放端口：`iptables -A INPUT -p tcp --dport 22 -j ACCEPT`

## 预防措施
- 配置服务自启动
- 备份配置文件
- 监控服务状态