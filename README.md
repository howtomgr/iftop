# iftop Installation Guide

iftop is a free and open-source bandwidth usage. iftop displays bandwidth usage on interface by host

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 1 core minimum
  - RAM: 128MB minimum
  - Storage: 100MB for data
  - Network: CLI tool
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port N/A (default iftop port)
  - None
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install iftop
sudo dnf install -y iftop

# Enable and start service
sudo systemctl enable --now iftop

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
iftop --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install iftop
sudo apt install -y iftop

# Enable and start service
sudo systemctl enable --now iftop

# Configure firewall
sudo ufw allow N/A

# Verify installation
iftop --version
```

### Arch Linux

```bash
# Install iftop
sudo pacman -S iftop

# Enable and start service
sudo systemctl enable --now iftop

# Verify installation
iftop --version
```

### Alpine Linux

```bash
# Install iftop
apk add --no-cache iftop

# Enable and start service
rc-update add iftop default
rc-service iftop start

# Verify installation
iftop --version
```

### openSUSE/SLES

```bash
# Install iftop
sudo zypper install -y iftop

# Enable and start service
sudo systemctl enable --now iftop

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
iftop --version
```

### macOS

```bash
# Using Homebrew
brew install iftop

# Start service
brew services start iftop

# Verify installation
iftop --version
```

### FreeBSD

```bash
# Using pkg
pkg install iftop

# Enable in rc.conf
echo 'iftop_enable="YES"' >> /etc/rc.conf

# Start service
service iftop start

# Verify installation
iftop --version
```

### Windows

```bash
# Using Chocolatey
choco install iftop

# Or using Scoop
scoop install iftop

# Verify installation
iftop --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/iftop

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
iftop --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable iftop

# Start service
sudo systemctl start iftop

# Stop service
sudo systemctl stop iftop

# Restart service
sudo systemctl restart iftop

# Check status
sudo systemctl status iftop

# View logs
sudo journalctl -u iftop -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add iftop default

# Start service
rc-service iftop start

# Stop service
rc-service iftop stop

# Restart service
rc-service iftop restart

# Check status
rc-service iftop status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'iftop_enable="YES"' >> /etc/rc.conf

# Start service
service iftop start

# Stop service
service iftop stop

# Restart service
service iftop restart

# Check status
service iftop status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start iftop
brew services stop iftop
brew services restart iftop

# Check status
brew services list | grep iftop
```

### Windows Service Manager

```powershell
# Start service
net start iftop

# Stop service
net stop iftop

# Using PowerShell
Start-Service iftop
Stop-Service iftop
Restart-Service iftop

# Check status
Get-Service iftop
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream iftop_backend {
    server 127.0.0.1:N/A;
}

server {
    listen 80;
    server_name iftop.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name iftop.example.com;

    ssl_certificate /etc/ssl/certs/iftop.example.com.crt;
    ssl_certificate_key /etc/ssl/private/iftop.example.com.key;

    location / {
        proxy_pass http://iftop_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName iftop.example.com
    Redirect permanent / https://iftop.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName iftop.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/iftop.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/iftop.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:N/A/
    ProxyPassReverse / http://127.0.0.1:N/A/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend iftop_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/iftop.pem
    redirect scheme https if !{ ssl_fc }
    default_backend iftop_backend

backend iftop_backend
    balance roundrobin
    server iftop1 127.0.0.1:N/A check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R iftop:iftop /etc/iftop
sudo chmod 750 /etc/iftop

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status iftop

# View logs
sudo journalctl -u iftop -f

# Monitor resource usage
top -p $(pgrep iftop)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/iftop"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/iftop-backup-$DATE.tar.gz" /etc/iftop /var/lib/iftop

echo "Backup completed: $BACKUP_DIR/iftop-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop iftop

# Restore from backup
tar -xzf /backup/iftop/iftop-backup-*.tar.gz -C /

# Start service
sudo systemctl start iftop
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u iftop -n 100
sudo tail -f /var/log/iftop/iftop.log

# Check configuration
iftop --version

# Check permissions
ls -la /etc/iftop
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep N/A

# Test connectivity
telnet localhost N/A

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep iftop)

# Check disk I/O
iotop -p $(pgrep iftop)

# Check connections
ss -an | grep N/A
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  iftop:
    image: iftop:latest
    ports:
      - "N/A:N/A"
    volumes:
      - ./config:/etc/iftop
      - ./data:/var/lib/iftop
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update iftop

# Debian/Ubuntu
sudo apt update && sudo apt upgrade iftop

# Arch Linux
sudo pacman -Syu iftop

# Alpine Linux
apk update && apk upgrade iftop

# openSUSE
sudo zypper update iftop

# FreeBSD
pkg update && pkg upgrade iftop

# Always backup before updates
tar -czf /backup/iftop-pre-update-$(date +%Y%m%d).tar.gz /etc/iftop

# Restart after updates
sudo systemctl restart iftop
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/iftop

# Clean old logs
find /var/log/iftop -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/iftop
```

## Additional Resources

- Official Documentation: https://docs.iftop.org/
- GitHub Repository: https://github.com/iftop/iftop
- Community Forum: https://forum.iftop.org/
- Best Practices Guide: https://docs.iftop.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
