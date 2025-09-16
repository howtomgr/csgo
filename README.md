# csgo Installation Guide

csgo is a free and open-source Counter-Strike server. Counter-Strike: Global Offensive dedicated server

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
  - Storage: 30GB for game
  - Network: Source protocol
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 27015 (default csgo port)
  - RCON available
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

# Install csgo
sudo dnf install -y csgo

# Enable and start service
sudo systemctl enable --now csgo

# Configure firewall
sudo firewall-cmd --permanent --add-port=27015/tcp
sudo firewall-cmd --reload

# Verify installation
csgo --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install csgo
sudo apt install -y csgo

# Enable and start service
sudo systemctl enable --now csgo

# Configure firewall
sudo ufw allow 27015

# Verify installation
csgo --version
```

### Arch Linux

```bash
# Install csgo
sudo pacman -S csgo

# Enable and start service
sudo systemctl enable --now csgo

# Verify installation
csgo --version
```

### Alpine Linux

```bash
# Install csgo
apk add --no-cache csgo

# Enable and start service
rc-update add csgo default
rc-service csgo start

# Verify installation
csgo --version
```

### openSUSE/SLES

```bash
# Install csgo
sudo zypper install -y csgo

# Enable and start service
sudo systemctl enable --now csgo

# Configure firewall
sudo firewall-cmd --permanent --add-port=27015/tcp
sudo firewall-cmd --reload

# Verify installation
csgo --version
```

### macOS

```bash
# Using Homebrew
brew install csgo

# Start service
brew services start csgo

# Verify installation
csgo --version
```

### FreeBSD

```bash
# Using pkg
pkg install csgo

# Enable in rc.conf
echo 'csgo_enable="YES"' >> /etc/rc.conf

# Start service
service csgo start

# Verify installation
csgo --version
```

### Windows

```bash
# Using Chocolatey
choco install csgo

# Or using Scoop
scoop install csgo

# Verify installation
csgo --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/csgo

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
csgo --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable csgo

# Start service
sudo systemctl start csgo

# Stop service
sudo systemctl stop csgo

# Restart service
sudo systemctl restart csgo

# Check status
sudo systemctl status csgo

# View logs
sudo journalctl -u csgo -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add csgo default

# Start service
rc-service csgo start

# Stop service
rc-service csgo stop

# Restart service
rc-service csgo restart

# Check status
rc-service csgo status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'csgo_enable="YES"' >> /etc/rc.conf

# Start service
service csgo start

# Stop service
service csgo stop

# Restart service
service csgo restart

# Check status
service csgo status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start csgo
brew services stop csgo
brew services restart csgo

# Check status
brew services list | grep csgo
```

### Windows Service Manager

```powershell
# Start service
net start csgo

# Stop service
net stop csgo

# Using PowerShell
Start-Service csgo
Stop-Service csgo
Restart-Service csgo

# Check status
Get-Service csgo
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream csgo_backend {
    server 127.0.0.1:27015;
}

server {
    listen 80;
    server_name csgo.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name csgo.example.com;

    ssl_certificate /etc/ssl/certs/csgo.example.com.crt;
    ssl_certificate_key /etc/ssl/private/csgo.example.com.key;

    location / {
        proxy_pass http://csgo_backend;
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
    ServerName csgo.example.com
    Redirect permanent / https://csgo.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName csgo.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/csgo.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/csgo.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:27015/
    ProxyPassReverse / http://127.0.0.1:27015/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend csgo_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/csgo.pem
    redirect scheme https if !{ ssl_fc }
    default_backend csgo_backend

backend csgo_backend
    balance roundrobin
    server csgo1 127.0.0.1:27015 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R csgo:csgo /etc/csgo
sudo chmod 750 /etc/csgo

# Configure firewall
sudo firewall-cmd --permanent --add-port=27015/tcp
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
sudo systemctl status csgo

# View logs
sudo journalctl -u csgo -f

# Monitor resource usage
top -p $(pgrep csgo)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/csgo"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/csgo-backup-$DATE.tar.gz" /etc/csgo /var/lib/csgo

echo "Backup completed: $BACKUP_DIR/csgo-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop csgo

# Restore from backup
tar -xzf /backup/csgo/csgo-backup-*.tar.gz -C /

# Start service
sudo systemctl start csgo
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u csgo -n 100
sudo tail -f /var/log/csgo/csgo.log

# Check configuration
csgo --version

# Check permissions
ls -la /etc/csgo
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 27015

# Test connectivity
telnet localhost 27015

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep csgo)

# Check disk I/O
iotop -p $(pgrep csgo)

# Check connections
ss -an | grep 27015
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  csgo:
    image: csgo:latest
    ports:
      - "27015:27015"
    volumes:
      - ./config:/etc/csgo
      - ./data:/var/lib/csgo
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update csgo

# Debian/Ubuntu
sudo apt update && sudo apt upgrade csgo

# Arch Linux
sudo pacman -Syu csgo

# Alpine Linux
apk update && apk upgrade csgo

# openSUSE
sudo zypper update csgo

# FreeBSD
pkg update && pkg upgrade csgo

# Always backup before updates
tar -czf /backup/csgo-pre-update-$(date +%Y%m%d).tar.gz /etc/csgo

# Restart after updates
sudo systemctl restart csgo
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/csgo

# Clean old logs
find /var/log/csgo -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/csgo
```

## Additional Resources

- Official Documentation: https://docs.csgo.org/
- GitHub Repository: https://github.com/csgo/csgo
- Community Forum: https://forum.csgo.org/
- Best Practices Guide: https://docs.csgo.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
