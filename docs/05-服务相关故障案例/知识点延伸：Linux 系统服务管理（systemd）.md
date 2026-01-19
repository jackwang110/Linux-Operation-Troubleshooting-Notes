# 知识点延伸：Linux 系统服务管理（systemd）

## 1. systemd 概述

### 1.1 什么是 systemd

**systemd** 是现代 Linux 系统的初始化系统和服务管理器，负责在系统启动时初始化硬件、挂载文件系统、启动系统服务等。它是大多数现代 Linux 发行版（如 Ubuntu 16.04+、CentOS 7+、Debian 8+ 等）的默认初始化系统，取代了传统的 SysV init 系统。

### 1.2 systemd 的主要特点

- **并行启动**：支持服务的并行启动，提高系统启动速度
- **按需启动**：支持服务的按需启动，减少系统资源占用
- **服务依赖管理**：自动处理服务之间的依赖关系
- **统一的配置格式**：使用 `.service` 文件统一管理服务配置
- **内置日志管理**：集成 journald 日志系统
- **控制组（cgroup）集成**：使用 cgroup 管理服务的资源使用
- **定时器功能**：替代传统的 cron 定时任务
- **快照功能**：支持系统状态的快照和恢复

### 1.3 systemd 的核心组件

- **systemd**：主守护进程，负责系统初始化和服务管理
- **journald**：日志管理服务，收集和存储系统日志
- **logind**：用户登录管理服务
- **networkd**：网络配置管理服务
- **resolved**：DNS 解析管理服务
- **timedated**：时间同步管理服务
- **timesyncd**：网络时间同步服务

## 2. systemd 服务管理基础

### 2.1 服务状态管理

**启动服务**：
```bash
# 启动服务
$ systemctl start <service-name>

# 示例：启动 nginx 服务
$ systemctl start nginx
```

**停止服务**：
```bash
# 停止服务
$ systemctl stop <service-name>

# 示例：停止 nginx 服务
$ systemctl stop nginx
```

**重启服务**：
```bash
# 重启服务
$ systemctl restart <service-name>

# 示例：重启 nginx 服务
$ systemctl restart nginx
```

**重新加载服务配置**：
```bash
# 重新加载服务配置
$ systemctl reload <service-name>

# 示例：重新加载 nginx 配置
$ systemctl reload nginx
```

**查看服务状态**：
```bash
# 查看服务状态
$ systemctl status <service-name>

# 示例：查看 nginx 服务状态
$ systemctl status nginx
```

### 2.2 服务自启动管理

**启用服务自启动**：
```bash
# 启用服务自启动
$ systemctl enable <service-name>

# 示例：启用 nginx 服务自启动
$ systemctl enable nginx
```

**禁用服务自启动**：
```bash
# 禁用服务自启动
$ systemctl disable <service-name>

# 示例：禁用 nginx 服务自启动
$ systemctl disable nginx
```

**查看服务自启动状态**：
```bash
# 查看服务自启动状态
$ systemctl is-enabled <service-name>

# 示例：查看 nginx 服务自启动状态
$ systemctl is-enabled nginx
```

**查看所有服务状态**：
```bash
# 查看所有服务状态
$ systemctl list-units --type=service

# 查看所有已启动的服务
$ systemctl list-units --type=service --state=running

# 查看所有自启动服务
$ systemctl list-unit-files --type=service | grep enabled
```

### 2.3 服务依赖管理

**查看服务依赖**：
```bash
# 查看服务依赖
$ systemctl list-dependencies <service-name>

# 示例：查看 nginx 服务依赖
$ systemctl list-dependencies nginx
```

**查看服务被依赖**：
```bash
# 查看服务被依赖
$ systemctl list-dependencies --reverse <service-name>

# 示例：查看 nginx 服务被依赖
$ systemctl list-dependencies --reverse nginx
```

### 2.4 服务日志管理

**查看服务日志**：
```bash
# 查看服务日志
$ journalctl -u <service-name>

# 示例：查看 nginx 服务日志
$ journalctl -u nginx
```

**查看服务日志（实时）**：
```bash
# 查看服务日志（实时）
$ journalctl -u <service-name> -f

# 示例：查看 nginx 服务日志（实时）
$ journalctl -u nginx -f
```

**查看服务日志（最近）**：
```bash
# 查看服务日志（最近）
$ journalctl -u <service-name> -n 100

# 示例：查看 nginx 服务日志（最近 100 行）
$ journalctl -u nginx -n 100
```

**查看服务日志（按时间）**：
```bash
# 查看服务日志（按时间）
$ journalctl -u <service-name> --since "2026-01-01 00:00:00" --until "2026-01-31 23:59:59"

# 示例：查看 nginx 服务 2026 年 1 月的日志
$ journalctl -u nginx --since "2026-01-01 00:00:00" --until "2026-01-31 23:59:59"
```

## 3. systemd 服务配置

### 3.1 服务配置文件结构

systemd 服务配置文件使用 `.service` 后缀，通常位于以下目录：

- `/etc/systemd/system/`：系统管理员创建的服务配置文件
- `/usr/lib/systemd/system/`：发行版提供的服务配置文件
- `/run/systemd/system/`：运行时生成的服务配置文件

### 3.2 服务配置文件格式

一个典型的 `.service` 文件包含以下几个部分：

```ini
[Unit]
Description=服务描述
After=依赖的服务
Requires=必须的服务

[Service]
Type=服务类型（simple、forking、oneshot、dbus、notify）
ExecStart=启动命令
ExecStop=停止命令
ExecReload=重新加载命令
Restart=重启策略（always、on-success、on-failure、on-abnormal、on-abort、on-watchdog）
RestartSec=重启间隔时间
User=运行用户
Group=运行组
WorkingDirectory=工作目录
Environment=环境变量

[Install]
WantedBy=目标（multi-user.target、graphical.target 等）
```

### 3.3 服务配置文件示例

**Nginx 服务配置文件示例**：

```ini
[Unit]
Description=Nginx Web Server
After=network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t -c /etc/nginx/nginx.conf
ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

**MySQL 服务配置文件示例**：

```ini
[Unit]
Description=MySQL Community Server
After=network.target

[Service]
Type=forking
PIDFile=/var/run/mysqld/mysqld.pid
ExecStartPre=/usr/share/mysql/mysql-systemd-start pre
ExecStart=/usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid
ExecStopPost=/usr/share/mysql/mysql-systemd-start post
Restart=on-failure
RestartPreventExitStatus=1

[Install]
WantedBy=multi-user.target
```

### 3.4 服务配置文件管理

**创建服务配置文件**：
```bash
# 创建服务配置文件
$ vi /etc/systemd/system/my-service.service
# 编辑服务配置文件内容
```

**重新加载服务配置**：
```bash
# 重新加载服务配置
$ systemctl daemon-reload

# 重启服务以应用新配置
$ systemctl restart my-service
```

**查看服务配置文件**：
```bash
# 查看服务配置文件
$ systemctl cat my-service

# 查看服务配置文件的位置
$ systemctl status my-service | grep Loaded
```

## 4. systemd 目标（Target）管理

### 4.1 什么是目标（Target）

目标（Target）是 systemd 中的一种特殊单元，用于表示系统的一种运行状态。它类似于传统 SysV init 系统中的运行级别（runlevel），但更加灵活。

### 4.2 常用目标（Target）

- **poweroff.target**：关机状态
- **rescue.target**：救援模式，提供最小化的系统环境
- **multi-user.target**：多用户模式，无图形界面
- **graphical.target**：图形界面模式
- **reboot.target**：重启状态
- **emergency.target**：紧急模式，提供最基本的系统环境

### 4.3 目标（Target）管理

**查看当前目标**：
```bash
# 查看当前目标
$ systemctl get-default

# 查看当前目标的详细信息
$ systemctl status
```

**设置默认目标**：
```bash
# 设置默认目标
$ systemctl set-default <target-name>

# 示例：设置默认目标为多用户模式
$ systemctl set-default multi-user.target
```

**切换目标**：
```bash
# 切换目标
$ systemctl isolate <target-name>

# 示例：切换到救援模式
$ systemctl isolate rescue.target
```

**查看目标依赖**：
```bash
# 查看目标依赖
$ systemctl list-dependencies <target-name>

# 示例：查看多用户模式的依赖
$ systemctl list-dependencies multi-user.target
```

## 5. systemd 定时器（Timer）

### 5.1 什么是定时器（Timer）

定时器（Timer）是 systemd 中的一种特殊单元，用于替代传统的 cron 定时任务。它可以精确控制任务的执行时间，并与服务单元集成。

### 5.2 定时器（Timer）配置

**定时器配置文件示例**：

```ini
[Unit]
Description=定时任务描述

[Timer]
OnCalendar=*-*-* *:*:00  # 执行时间（每小时执行）
Persistent=true  # 系统启动后是否执行错过的任务

[Install]
WantedBy=timers.target
```

**对应的服务配置文件**：

```ini
[Unit]
Description=定时任务服务

[Service]
Type=oneshot
ExecStart=/path/to/script.sh

[Install]
WantedBy=multi-user.target
```

### 5.3 定时器（Timer）管理

**查看定时器状态**：
```bash
# 查看所有定时器
$ systemctl list-timers

# 查看特定定时器状态
$ systemctl status <timer-name>
```

**启动定时器**：
```bash
# 启动定时器
$ systemctl start <timer-name>

# 启用定时器自启动
$ systemctl enable <timer-name>
```

**停止定时器**：
```bash
# 停止定时器
$ systemctl stop <timer-name>

# 禁用定时器自启动
$ systemctl disable <timer-name>
```

## 6. systemd 资源管理

### 6.1 控制组（cgroup）集成

systemd 使用 Linux 控制组（cgroup）来管理服务的资源使用，包括 CPU、内存、磁盘 I/O 和网络带宽等。

### 6.2 资源限制配置

**CPU 限制**：
```ini
[Service]
CPUAccounting=true
CPUQuota=50%  # 限制 CPU 使用率为 50%
```

**内存限制**：
```ini
[Service]
MemoryAccounting=true
MemoryLimit=512M  # 限制内存使用为 512MB
```

**磁盘 I/O 限制**：
```ini
[Service]
IOAccounting=true
IOSchedulingClass=best-effort  # I/O 调度类别
IOSchedulingPriority=4  # I/O 调度优先级
```

**网络带宽限制**：
```ini
[Service]
IPAccounting=true
```

### 6.3 资源使用监控

**查看服务资源使用**：
```bash
# 查看服务 CPU 和内存使用
$ systemctl status <service-name>

# 查看服务详细资源使用
$ systemd-cgtop
```

## 7. systemd 故障排查

### 7.1 服务启动失败排查

**查看服务状态**：
```bash
# 查看服务状态
$ systemctl status <service-name>

# 查看服务启动失败的原因
$ journalctl -xe

# 查看服务日志
$ journalctl -u <service-name>
```

**检查服务配置文件**：
```bash
# 检查服务配置文件语法
$ systemctl cat <service-name>

# 重新加载服务配置
$ systemctl daemon-reload

# 重启服务
$ systemctl restart <service-name>
```

**检查服务依赖**：
```bash
# 查看服务依赖
$ systemctl list-dependencies <service-name>

# 检查依赖服务状态
$ systemctl status <dependency-service>
```

### 7.2 服务性能问题排查

**查看服务资源使用**：
```bash
# 查看服务 CPU 和内存使用
$ systemctl status <service-name>

# 查看服务详细资源使用
$ systemd-cgtop

# 查看服务进程
$ ps aux | grep <service-name>
```

**查看服务日志**：
```bash
# 查看服务日志
$ journalctl -u <service-name>

# 查看服务错误日志
$ journalctl -u <service-name> | grep error
```

### 7.3 系统启动问题排查

**查看系统启动日志**：
```bash
# 查看系统启动日志
$ journalctl -b

# 查看系统启动失败的服务
$ systemctl --failed

# 查看系统启动时间
$ systemd-analyze

# 查看系统启动耗时分析
$ systemd-analyze blame

# 查看系统启动依赖图
$ systemd-analyze plot > boot.svg
```

## 8. systemd 最佳实践

### 8.1 服务配置最佳实践

1. **使用标准配置目录**：将自定义服务配置文件放在 `/etc/systemd/system/` 目录
2. **遵循配置文件格式**：使用标准的 `.service` 文件格式，确保语法正确
3. **设置合适的服务类型**：根据服务的启动方式选择合适的 `Type`（simple、forking、oneshot 等）
4. **配置重启策略**：根据服务的重要性设置合适的 `Restart` 策略
5. **设置资源限制**：根据服务的需求设置合适的资源限制
6. **使用环境变量**：通过 `Environment` 或 `EnvironmentFile` 管理服务的环境变量
7. **配置日志管理**：确保服务日志被正确收集和存储

### 8.2 服务管理最佳实践

1. **使用 systemctl 命令**：使用 `systemctl` 命令管理服务，避免直接操作进程
2. **启用服务自启动**：对于重要服务，启用自启动以确保系统重启后服务自动运行
3. **定期检查服务状态**：定期检查服务状态，确保服务正常运行
4. **监控服务日志**：监控服务日志，及时发现潜在问题
5. **备份服务配置**：定期备份服务配置文件，以防止配置丢失
6. **使用版本控制**：使用版本控制工具管理服务配置文件的变更
7. **测试服务配置**：在修改服务配置后，测试服务是否能正常启动和运行

### 8.3 系统管理最佳实践

1. **设置合适的默认目标**：根据系统用途设置合适的默认目标（multi-user.target 或 graphical.target）
2. **优化启动顺序**：通过调整服务依赖关系，优化系统启动顺序
3. **使用定时器替代 cron**：对于需要精确控制的定时任务，使用 systemd 定时器
4. **监控系统资源**：使用 systemd-cgtop 监控系统资源使用情况
5. **定期清理日志**：定期清理 journald 日志，避免日志占用过多磁盘空间
6. **使用快照功能**：在系统变更前创建快照，以便在出现问题时恢复
7. **遵循最小化原则**：只启用必要的服务，减少系统资源占用

## 9. 总结

systemd 是现代 Linux 系统的核心组件，提供了强大的服务管理、资源管理和系统初始化功能。通过本文介绍的 systemd 服务管理基础、配置方法和最佳实践，希望读者能够更好地理解和应用 systemd，提高 Linux 系统的管理效率和可靠性。

在实际系统管理中，需要根据具体的服务需求和系统环境，合理配置和管理 systemd 服务。同时，通过定期的检查和监控，可以预防和减少服务故障的发生，提高系统的稳定性和安全性。

systemd 的设计理念是统一管理系统的各种资源和服务，提供更加灵活和强大的系统管理能力。随着 Linux 发行版的不断更新，systemd 的功能也在不断完善，成为现代 Linux 系统管理的标准工具。