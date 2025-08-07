# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This repository contains Linux network speed optimization scripts focused on BBR, BBRplus, and LotServer (锐速) implementations. The project provides automated kernel installation and network congestion control optimization for CentOS, Debian, Ubuntu, and Alpine Linux systems.

## Main Scripts Architecture

The codebase consists of several bash scripts with specific purposes:

### Core Scripts
- `tcp.sh` (v100.0.4.2): Main script that installs/uninstalls kernels and removes old kernel versions after optimization
- `tcpx.sh` (v100.0.4.5): Alternative script that preserves existing kernels while applying network optimizations
- `InstallNET.sh`: Network-based system reinstallation script (by MoeClub.org)
- `Debian_Kernel.sh`: Debian-specific kernel management utilities
- `c7_tcp_react.sh`: CentOS 7 specific TCP optimization script

### Directory Structure
- `bbr/`: Contains kernel packages for BBR algorithm support
  - `centos/`: CentOS RPM kernel packages (6, 7) 
  - `debian-ubuntu/`: DEB kernel packages for Debian/Ubuntu
- `bbrplus/`: Contains kernel packages with BBRplus algorithm
- `lotserver/`: Contains kernel packages compatible with LotServer
- Source files: `tcp_*.c` - TCP congestion control algorithm source code

## Usage Commands

### Primary Installation Commands
```bash
# Install with kernel removal (recommended for production)
wget -O tcp.sh "https://github.com/ChasLui/Linux-NetSpeed/raw/master/tcp.sh" && chmod +x tcp.sh && ./tcp.sh

# Install without kernel removal (safer for testing)
wget -O tcpx.sh "https://github.com/ChasLui/Linux-NetSpeed/raw/master/tcpx.sh" && chmod +x tcpx.sh && ./tcpx.sh

# Direct optimization (bypasses system detection)
./tcpx.sh op
./tcpx.sh op2
```

### System Preparation
```bash
# CentOS
yum install ca-certificates wget -y && update-ca-trust force-enable

# Debian/Ubuntu  
apt-get install ca-certificates wget -y && update-ca-certificates

# Alpine Linux 3.20+
apk update && apk add ca-certificates wget && update-ca-certificates
```

### Verification Commands
```bash
# Check current TCP congestion control
cat /proc/sys/net/ipv4/tcp_congestion_control

# Check available TCP algorithms
cat /proc/sys/net/ipv4/tcp_allowed_congestion_control

# Check current qdisc
sysctl net.core.default_qdisc

# Check BBR module (no output expected for built-in BBR)
lsmod | grep bbr

# Check LotServer status
bash /appex/bin/lotServer.sh status | grep "LotServer"

# View queue statistics
tc -s qdisc show
```

## Architecture Notes

- Scripts require root privileges and perform system-level kernel modifications
- Configuration modifications are applied to `/etc/sysctl.d/99-sysctl.conf`
- Kernel packages are architecture-specific (x32/x64) and distribution-specific
- The main scripts include extensive system detection and validation logic
- Version management is handled through variable `sh_ver` in each script
- Network optimization parameters are applied through sysctl configuration
- **Alpine Linux Support**: Added in latest version, uses `apk` package manager and linux-lts kernel
  - Alpine 3.20+ supported with BBR configuration
  - BBRplus functionality falls back to standard BBR on Alpine
  - Uses `/etc/modules-load.d/bbr.conf` for module loading

## Development Context

This is a network optimization tool repository, not a traditional software development project. There are no build systems, test frameworks, or linting tools - the scripts are meant to be executed directly on target systems for network performance optimization.

## Alpine Linux Support Details

As of the latest updates, the project now provides comprehensive Alpine Linux support:

### Supported Scripts
- **tcp.sh**: Full Alpine 3.20+ support with automatic detection and `installbbr_alpine()` function
- **tcpx.sh**: Complete Alpine support with all optimization functions (`installbbr_alpine`, `installbbrplus_alpine`, `installxanmod_alpine`, etc.)
- **InstallNET.sh**: Added `-a/--alpine` parameter for Alpine system installation
- **Debian_Kernel.sh**: Added Alpine detection and kernel installation via `apk`
- **c7_tcp_react.sh**: Alpine detection with appropriate fallback messages

### Alpine-Specific Features
- Automatic version checking (requires 3.20+)
- Uses `linux-lts` kernel package from official repositories
- BBR configuration via `/etc/sysctl.d/99-sysctl.conf`
- Module loading via `/etc/modules-load.d/bbr.conf`  
- All advanced kernels (BBRplus, xanmod) gracefully fall back to standard BBR
- Dedicated `alpine/` directory with configuration examples

### File Structure
- `alpine/configs/bbr-sysctl.conf` - Complete BBR sysctl configuration
- `alpine/configs/modules-bbr.conf` - Kernel module loading configuration  
- `alpine/README.md` - Alpine-specific documentation and usage instructions