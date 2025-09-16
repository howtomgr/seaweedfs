# seaweedfs Installation Guide

seaweedfs is a free and open-source distributed storage. SeaweedFS provides simple and highly scalable distributed file system

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
  - CPU: 2+ cores
  - RAM: 2GB minimum
  - Storage: 100GB+ for data
  - Network: HTTP/gRPC
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 9333 (default seaweedfs port)
  - Volume on 8080
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

# Install seaweedfs
sudo dnf install -y seaweedfs

# Enable and start service
sudo systemctl enable --now seaweedfs

# Configure firewall
sudo firewall-cmd --permanent --add-port=9333/tcp
sudo firewall-cmd --reload

# Verify installation
seaweedfs --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install seaweedfs
sudo apt install -y seaweedfs

# Enable and start service
sudo systemctl enable --now seaweedfs

# Configure firewall
sudo ufw allow 9333

# Verify installation
seaweedfs --version
```

### Arch Linux

```bash
# Install seaweedfs
sudo pacman -S seaweedfs

# Enable and start service
sudo systemctl enable --now seaweedfs

# Verify installation
seaweedfs --version
```

### Alpine Linux

```bash
# Install seaweedfs
apk add --no-cache seaweedfs

# Enable and start service
rc-update add seaweedfs default
rc-service seaweedfs start

# Verify installation
seaweedfs --version
```

### openSUSE/SLES

```bash
# Install seaweedfs
sudo zypper install -y seaweedfs

# Enable and start service
sudo systemctl enable --now seaweedfs

# Configure firewall
sudo firewall-cmd --permanent --add-port=9333/tcp
sudo firewall-cmd --reload

# Verify installation
seaweedfs --version
```

### macOS

```bash
# Using Homebrew
brew install seaweedfs

# Start service
brew services start seaweedfs

# Verify installation
seaweedfs --version
```

### FreeBSD

```bash
# Using pkg
pkg install seaweedfs

# Enable in rc.conf
echo 'seaweedfs_enable="YES"' >> /etc/rc.conf

# Start service
service seaweedfs start

# Verify installation
seaweedfs --version
```

### Windows

```bash
# Using Chocolatey
choco install seaweedfs

# Or using Scoop
scoop install seaweedfs

# Verify installation
seaweedfs --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/seaweedfs

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
seaweedfs --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable seaweedfs

# Start service
sudo systemctl start seaweedfs

# Stop service
sudo systemctl stop seaweedfs

# Restart service
sudo systemctl restart seaweedfs

# Check status
sudo systemctl status seaweedfs

# View logs
sudo journalctl -u seaweedfs -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add seaweedfs default

# Start service
rc-service seaweedfs start

# Stop service
rc-service seaweedfs stop

# Restart service
rc-service seaweedfs restart

# Check status
rc-service seaweedfs status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'seaweedfs_enable="YES"' >> /etc/rc.conf

# Start service
service seaweedfs start

# Stop service
service seaweedfs stop

# Restart service
service seaweedfs restart

# Check status
service seaweedfs status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start seaweedfs
brew services stop seaweedfs
brew services restart seaweedfs

# Check status
brew services list | grep seaweedfs
```

### Windows Service Manager

```powershell
# Start service
net start seaweedfs

# Stop service
net stop seaweedfs

# Using PowerShell
Start-Service seaweedfs
Stop-Service seaweedfs
Restart-Service seaweedfs

# Check status
Get-Service seaweedfs
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream seaweedfs_backend {
    server 127.0.0.1:9333;
}

server {
    listen 80;
    server_name seaweedfs.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name seaweedfs.example.com;

    ssl_certificate /etc/ssl/certs/seaweedfs.example.com.crt;
    ssl_certificate_key /etc/ssl/private/seaweedfs.example.com.key;

    location / {
        proxy_pass http://seaweedfs_backend;
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
    ServerName seaweedfs.example.com
    Redirect permanent / https://seaweedfs.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName seaweedfs.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/seaweedfs.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/seaweedfs.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:9333/
    ProxyPassReverse / http://127.0.0.1:9333/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend seaweedfs_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/seaweedfs.pem
    redirect scheme https if !{ ssl_fc }
    default_backend seaweedfs_backend

backend seaweedfs_backend
    balance roundrobin
    server seaweedfs1 127.0.0.1:9333 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R seaweedfs:seaweedfs /etc/seaweedfs
sudo chmod 750 /etc/seaweedfs

# Configure firewall
sudo firewall-cmd --permanent --add-port=9333/tcp
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
sudo systemctl status seaweedfs

# View logs
sudo journalctl -u seaweedfs -f

# Monitor resource usage
top -p $(pgrep seaweedfs)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/seaweedfs"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/seaweedfs-backup-$DATE.tar.gz" /etc/seaweedfs /var/lib/seaweedfs

echo "Backup completed: $BACKUP_DIR/seaweedfs-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop seaweedfs

# Restore from backup
tar -xzf /backup/seaweedfs/seaweedfs-backup-*.tar.gz -C /

# Start service
sudo systemctl start seaweedfs
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u seaweedfs -n 100
sudo tail -f /var/log/seaweedfs/seaweedfs.log

# Check configuration
seaweedfs --version

# Check permissions
ls -la /etc/seaweedfs
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 9333

# Test connectivity
telnet localhost 9333

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep seaweedfs)

# Check disk I/O
iotop -p $(pgrep seaweedfs)

# Check connections
ss -an | grep 9333
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  seaweedfs:
    image: seaweedfs:latest
    ports:
      - "9333:9333"
    volumes:
      - ./config:/etc/seaweedfs
      - ./data:/var/lib/seaweedfs
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update seaweedfs

# Debian/Ubuntu
sudo apt update && sudo apt upgrade seaweedfs

# Arch Linux
sudo pacman -Syu seaweedfs

# Alpine Linux
apk update && apk upgrade seaweedfs

# openSUSE
sudo zypper update seaweedfs

# FreeBSD
pkg update && pkg upgrade seaweedfs

# Always backup before updates
tar -czf /backup/seaweedfs-pre-update-$(date +%Y%m%d).tar.gz /etc/seaweedfs

# Restart after updates
sudo systemctl restart seaweedfs
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/seaweedfs

# Clean old logs
find /var/log/seaweedfs -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/seaweedfs
```

## Additional Resources

- Official Documentation: https://docs.seaweedfs.org/
- GitHub Repository: https://github.com/seaweedfs/seaweedfs
- Community Forum: https://forum.seaweedfs.org/
- Best Practices Guide: https://docs.seaweedfs.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
