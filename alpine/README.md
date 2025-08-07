# Alpine Linux 内核包目录

本目录包含 Alpine Linux 3.20+ 系统使用的内核包和配置文件。

## 目录结构

- `x64/` - 64位系统的内核包
- `configs/` - Alpine Linux BBR 配置文件

## 说明

Alpine Linux 使用 `apk` 包管理器，内核包通过官方仓库安装：

- `linux-lts` - 长期支持版内核，默认支持 BBR
- `linux-firmware` - 硬件固件包

## 使用方法

```bash
# 更新包索引
apk update

# 安装 LTS 内核
apk add linux-lts linux-firmware

# 配置 BBR
echo "net.core.default_qdisc=fq" >> /etc/sysctl.d/99-sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.d/99-sysctl.conf
sysctl -p

# 重启系统
reboot
```

## 支持的版本

- Alpine Linux 3.20+
- 架构: x86_64 (amd64)