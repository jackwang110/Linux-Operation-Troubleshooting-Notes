# 案例2：MySQL 无法连接（权限/端口/进程状态）

## 现象
- MySQL 客户端无法连接到服务器
- 连接超时
- 权限错误

## 排查步骤
1. 检查服务状态：`systemctl status mysql`
2. 检查端口监听：`netstat -tuln | grep 3306`
3. 检查权限：`mysql -u root -p`

## 根因分析
- 服务未启动
- 端口未开放
- 权限配置错误

## 解决方案
- 启动服务：`systemctl start mysql`
- 开放端口：`iptables -A INPUT -p tcp --dport 3306 -j ACCEPT`
- 修复权限：`GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'password' WITH GRANT OPTION;`

## 预防措施
- 配置服务自启动
- 优化权限配置
- 监控服务状态