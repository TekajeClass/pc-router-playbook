# PC Router Ansible Playbook

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Ansible](https://img.shields.io/badge/Ansible-2.9+-blue.svg)](https://ansible.com/)
[![Debian](https://img.shields.io/badge/Debian-A81D33?style=flat&logo=debian&logoColor=white)](https://www.debian.org/)

**Otomatisasi konfigurasi PC/Linux sebagai router dengan DHCP server menggunakan Ansible**

> Transform your Linux machine into a fully functional router with DHCP server capabilities through automated Ansible deployment.

## 📋 Daftar Isi

- [Tentang Proyek](#tentang-proyek)
- [Arsitektur](#arsitektur)
- [Fitur](#fitur)
- [Persyaratan](#persyaratan)
- [Instalasi](#instalasi)
- [Penggunaan](#penggunaan)
- [Konfigurasi](#konfigurasi)
- [Verifikasi Setup](#verifikasi-setup)
- [Troubleshooting](#troubleshooting)
- [FAQ](#faq)
- [Kontribusi](#kontribusi)
- [Lisensi](#lisensi)
- [Changelog](#changelog)

## 📝 Tentang Proyek

Proyek ini menyediakan Ansible Playbook untuk mengkonfigurasi komputer berbasis Linux menjadi router dengan DHCP server. Playbook ini mengotomatisasi seluruh proses setup termasuk instalasi paket, konfigurasi network interface, pengaturan DHCP, dan konfigurasi iptables untuk Network Address Translation (NAT).

**🎯 Use Case:**
- Setup router di lingkungan lab/testing
- Konfigurasi PC sebagai gateway untuk jaringan kecil
- Otomasi deployment network infrastructure
- Learning tool untuk networking dan automation

**Disclaimer**: Script ini dibuat 97% tanpa AI. AI hanya digunakan untuk membantu menjelaskan dokumentasi dari module Ansible.

## 🏗️ Arsitektur

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Internet      │────│   WAN Interface │────│   PC-Router     │
│                 │    │   (ens33)       │    │   (Ansible)     │
└─────────────────┘    └─────────────────┘    └─────────────────┘
                                                     │
                                                     │ LAN Interface
                                                     │ (ens37)
                                                     │
┌─────────────────┐                           ┌─────────────────┐
│   Client 1      │───────────────────────────│    Switch/Hub   │
│   (DHCP Client) │                           │   192.168.100.x │
└─────────────────┘                           └─────────────────┘
┌─────────────────┐                                   │
│   Client 2      │───────────────────────────────────┘
│   (DHCP Client) │
└─────────────────┘
```

**Network Flow:**
1. **Internet** → **WAN Interface** → **NAT (iptables)** → **LAN Interface**
2. **DHCP Requests** → **ISC DHCP Server** → **IP Assignment**
3. **Client Traffic** → **Router** → **Internet** (via MASQUERADE)

## ✨ Fitur

- ✅ **Instalasi Otomatis** ISC DHCP Server & iptables
- ✅ **Konfigurasi Interface** jaringan sekunder otomatis
- ✅ **DHCP Pool Dinamis** dengan parameter interaktif
- ✅ **IPv4 Forwarding** aktivasi otomatis
- ✅ **NAT Configuration** menggunakan iptables MASQUERADE
- ✅ **Template-based Setup** menggunakan Jinja2
- ✅ **Error Handling** yang robust
- ✅ **Idempotent** - dapat dijalankan berulang tanpa masalah
- ✅ **Cross-platform** support untuk Debian

## 📦 Persyaratan

### 🔧 Sistem Target (Router Machine)
- **OS**: Linux berbasis Debian
  - Debian 9 (Stretch) atau lebih baru
- **Hardware**:
  - Minimal 2 Network Interface Cards (NIC)
  - RAM: 512MB minimum, 1GB recommended
  - Storage: 5GB minimum
- **Akses**: Root atau sudo privileges
- **Network**: Koneksi internet untuk instalasi paket

### 💻 Sistem Host (Ansible Controller)
- **Ansible**: v2.9 atau lebih baru
- **Python**: 3.6 atau lebih baru
- **SSH Client**: Untuk koneksi ke target machine
- **OS**: Linux, macOS, atau Windows (dengan WSL)

### 📚 Ansible Collections Required
```bash
ansible-galaxy collection install ansible.posix
```

### 📥 Paket yang Akan Diinstal Otomatis
- `isc-dhcp-server` - ISC DHCP Server untuk IP assignment
- `iptables` - Linux firewall & NAT tools
- `ansible.posix.sysctl` - System control management

## 🚀 Instalasi

### 1. Clone Repository

```bash
git clone https://github.com/yourusername/ansible-pc-router.git
cd ansible-pc-router
```

### 2. Install Ansible

**Pada Debian:**
```bash
sudo apt-get update
sudo apt-get install ansible python3-pip
```

**Menggunakan pip:**
```bash
pip3 install ansible
```

**Verifikasi instalasi:**
```bash
ansible --version
# Output: ansible [core 2.15.0]
```

### 3. Install Required Collections

```bash
ansible-galaxy collection install ansible.posix
```

### 4. Setup SSH Access

**Generate SSH key (jika belum ada):**
```bash
ssh-keygen -t rsa -b 4096 -C "your-email@example.com"
```

**Copy SSH key ke target machine:**
```bash
ssh-copy-id username@target-ip
```

### 5. Configure Inventory

Buat file `inventory.ini`:

```ini
[routers]
router-01 ansible_host=192.168.1.100 ansible_user=debian ansible_port=22

[routers:vars]
ansible_python_interpreter=/usr/bin/python3
```

## 💻 Penggunaan

### Basic Execution

```bash
# Keluarkan script yaml dari folder pc-router-playbook
mv pc-router-playbook/pc-router-ansible.yaml .
# Menggunakan inventory file
ansible-playbook -i inventory.ini pc-router-ansible.yaml

# Menggunakan host langsung
ansible-playbook -i "router-01," pc-router-ansible.yaml
```

### Advanced Options

```bash
# Verbose output
ansible-playbook -i inventory.ini pc-router-ansible.yaml -vv

# Dry-run (check mode)
ansible-playbook -i inventory.ini pc-router-ansible.yaml --check

# Limit ke host tertentu
ansible-playbook -i inventory.ini pc-router-ansible.yaml -l router-01

# Menggunakan vault untuk sensitive data
ansible-playbook -i inventory.ini pc-router-ansible.yaml --ask-vault-pass
```

### Contoh Output Playbook

```
PLAY [PC Router Configuration Playbook] **********************************

TASK [TENTANG CREATOR] **************************************************
Pausing for 3 seconds
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
This Playbook Created by analuaM sagaB | Universitas Bani Saleh
ok: [router-01]

TASK [DISCLAIMER DARI CREATOR] ******************************************
Pausing for 7 seconds
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
DISCLAIMER: SCRIPT INI 90% DIBUAT TANPA AI...
ok: [router-01]

TASK [Update repository] ************************************************
ok: [router-01]

TASK [Installing Package (1/2)] *****************************************
changed: [router-01]

TASK [Installing Package (2/2)] *****************************************
changed: [router-01]

... (tasks lainnya)

PLAY RECAP **************************************************************
router-01                  : ok=12   changed=8   unreachable=0   failed=0
```

## ⚙️ Konfigurasi

Playbook ini menggunakan **interactive prompts** untuk parameter konfigurasi:

| Parameter | Contoh Input | Deskripsi | Validasi |
|-----------|--------------|-----------|----------|
| `wan_interface` | `ens33` | Interface WAN (internet) | Interface harus exist |
| `secondary_interface` | `ens37` | Interface LAN | Interface harus exist |
| `secondary_ip_addr` | `192.168.100.1/24` | IP LAN dengan CIDR | Format CIDR valid |
| `dhcp_network` | `192.168.100.0` | Network DHCP pool | IP network valid |
| `dhcp_netmask` | `255.255.255.0` | Netmask DHCP | Netmask valid |
| `dhcp_range` | `192.168.100.10 192.168.100.200` | Range IP client | Dalam network range |
| `dhcp_dns` | `8.8.8.8, 1.1.1.1` | DNS servers | IP valid |
| `dhcp_gateway` | `192.168.100.1` | Gateway IP | Sama dengan LAN IP |
| `dhcp_broadcast` | `192.168.100.255` | Broadcast address | Broadcast valid |

### Contoh Konfigurasi Lengkap

```
Masukkan interface yang mengarah ke public (Contoh : ens33): ens33
Masukkan interface kedua anda (Contoh : ens37): ens37
Masukkan ip address interface kedua dengan prefix (Contoh : 100.100.10.1/24): 192.168.1.1/24
Masukkan network untuk dhcp pool anda (Contoh : 100.100.10.0): 192.168.1.0
Masukkan netmask untuk dhcp pool anda (Contoh : 255.255.255.0): 255.255.255.0
Masukkan range untuk dhcp pool anda (Contoh : 100.100.10.2 100.100.10.254): 192.168.1.100 192.168.1.200
Masukkan nameserver/DNS untuk dhcp pool anda (Contoh : 8.8.8.8): 8.8.8.8, 8.8.4.4
Masukkan gateway untuk dhcp pool anda (Contoh : 100.100.10.1): 192.168.1.1
Masukkan broadcast address untuk dhcp pool anda (Contoh : 100.100.10.255): 192.168.1.255
```

## 📁 Struktur File

```
ansible-pc-router/
├── 📄 README.md                    # Dokumentasi lengkap
├── 📄 pc-router-ansible.yaml       # Main playbook
├── 📄 dhcpd.conf                   # DHCP server template
└── 📁 default/                      # Ansible roles (future enhancement)
     └── 📄 isc-dhcp-server             # ISC DHCP defaults template
```

## 🔧 Tasks yang Dijalankan

Playbook menjalankan **12 tasks** secara berurutan:

1. **ℹ️ Creator Info** - Menampilkan informasi pembuat
2. **⚠️ Disclaimer** - Informasi penggunaan AI
3. **🔄 Repository Update** - Update package cache
4. **📦 Package Installation** - Install ISC DHCP & iptables
5. **🌐 Network Configuration** - Setup secondary interface
6. **📝 DHCP Config Injection** - Deploy DHCP configuration
7. **⚙️ DHCP Defaults Setup** - Configure ISC DHCP defaults
8. **🚀 IPv4 Forwarding** - Enable packet forwarding
9. **🔥 NAT Configuration** - Setup iptables MASQUERADE
10. **🔄 Service Restart** - Restart DHCP service

## ✅ Verifikasi Setup

### 1. Cek Status DHCP Server
```bash
sudo systemctl status isc-dhcp-server
# Expected: Active (running)
```

### 2. Verifikasi Network Interfaces
```bash
ip addr show
# ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP
# ens37: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP
```

### 3. Cek IPv4 Forwarding
```bash
cat /proc/sys/net/ipv4/ip_forward
# Expected output: 1
```

### 4. Verifikasi iptables NAT Rules
```bash
sudo iptables -t nat -L -n -v
# Chain POSTROUTING (policy ACCEPT)
# MASQUERADE  all  --  *      ens33   0.0.0.0/0            0.0.0.0/0
```

### 5. Test DHCP dari Client
```bash
# Pada client machine
sudo dhclient ens37  # atau interface yang sesuai
ip addr show ens37   # Cek IP yang didapat
```

### 6. Test Connectivity
```bash
# Dari client, test koneksi internet
ping 8.8.8.8
curl ifconfig.me  # Cek public IP
```

## 🐛 Troubleshooting

### DHCP Server Tidak Berjalan
```bash
# Check service status
sudo systemctl status isc-dhcp-server

# Check logs
sudo journalctl -u isc-dhcp-server -n 50 --no-pager

# Verify configuration syntax
sudo dhcpd -t -cf /etc/dhcp/dhcpd.conf

# Restart service
sudo systemctl restart isc-dhcp-server
```

### Interface Tidak Mendapat IP
```bash
# Restart networking service
sudo systemctl restart networking

# Atau restart NetworkManager
sudo systemctl restart NetworkManager

# Manual interface restart
sudo ifdown ens37 && sudo ifup ens37
```

### Ansible Connection Failed
```bash
# Test SSH connection
ssh -v username@target-ip

# Check Ansible inventory
ansible-inventory -i inventory.ini --list

# Test Ansible connectivity
ansible -i inventory.ini routers -m ping
```

### NAT Tidak Bekerja
```bash
# Check iptables rules
sudo iptables -t nat -L -n -v

# Verify forwarding is enabled
sysctl net.ipv4.ip_forward

# Check for conflicting firewall rules
sudo iptables -L -n -v
```

### Package Installation Failed
```bash
# Update package cache
sudo apt-get update

# Fix broken packages
sudo apt-get install -f

# Clear package cache
sudo apt-get clean && sudo apt-get autoclean
```

## ❓ FAQ

**Q: Apakah playbook ini bisa digunakan di distro Linux lain?**  
A: Saat ini hanya mendukung Debian. Untuk distro lain (RHEL/CentOS), perlu modifikasi package manager dan service names.

**Q: Bagaimana cara rollback konfigurasi?**  
A: Backup file konfigurasi sebelum menjalankan playbook. Gunakan `ansible-playbook --check` untuk dry-run.

**Q: Apakah mendukung IPv6?**  
A: Saat ini hanya IPv4. IPv6 support dapat ditambahkan di versi mendatang.

**Q: Berapa banyak client yang bisa dilayani?**  
A: Tergantung hardware router. Untuk small office (10-50 client) cukup dengan spesifikasi minimum.

**Q: Apakah bisa menggunakan static IP untuk beberapa client?**  
A: Ya, edit file `dhcpd.conf` template untuk menambahkan host declarations.

## 🤝 Kontribusi

Kontribusi sangat diterima! 🚀

### Development Setup

```bash
# Fork dan clone repository
git clone https://github.com/yourusername/ansible-pc-router.git
cd ansible-pc-router

# Setup virtual environment
python3 -m venv venv
source venv/bin/activate

# Install development dependencies
pip install ansible-lint yamllint

# Run linting
ansible-lint pc-router-ansible.yaml
yamllint pc-router-ansible.yaml
```

### Contributing Guidelines

1. **Fork** repository ini
2. **Buat branch** fitur: `git checkout -b feature/AmazingFeature`
3. **Commit** perubahan: `git commit -m 'Add some AmazingFeature'`
4. **Push** ke branch: `git push origin feature/AmazingFeature`
5. **Buat Pull Request**

### Code Standards

- ✅ Gunakan YAML formatting yang konsisten
- ✅ Tambahkan komentar pada tasks kompleks
- ✅ Test playbook dengan `--check` mode
- ✅ Update dokumentasi untuk perubahan signifikan
- ✅ Ikuti Ansible best practices

## 📜 Lisensi

**MIT License** - Lihat file [LICENSE](LICENSE) untuk detail lengkap.

```
Copyright (c) 2026 Bagas Maulana

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.
```

## 👥 Kontributor

### Original Creator
**Bagas Maulana**  
🎓 Universitas Bani Saleh  
📧 [Email](mailto:fopensource.ubs@gmail.com)  
🐙 [GitHub](https://github.com/analuamsagab)

### Acknowledgments
- ISC DHCP Project
- Ansible Community
- Debian Communities

## 📈 Changelog

### [v1.0.0] - 2024-12-19
- ✅ Initial release
- ✅ Basic router configuration with DHCP
- ✅ Interactive parameter input
- ✅ IPv4 forwarding and NAT setup
- ✅ Comprehensive documentation

### [v0.9.0] - 2024-12-01 (Beta)
- 🔄 Template-based configuration
- 🔄 Error handling improvements
- 🔄 Documentation updates

## 🆘 Support

### Getting Help

1. **📖 Documentation**: Baca README.md dan comments di playbook
2. **🐛 Issues**: Buat issue di [GitHub Issues](https://github.com/TekajeClass/ansible-pc-router/issues)
3. **💬 Discussions**: Gunakan [GitHub Discussions](https://github.com/TekajeClass/ansible-pc-router/discussions)
4. **📧 Email**: Contact maintainer

### Reporting Bugs

Gunakan template issue untuk bug reports:
- Deskripsi masalah
- Steps to reproduce
- Expected vs actual behavior
- System information
- Log output (jika ada)

## ⚠️ Important Notes

- 🔐 **Security**: Playbook memerlukan akses root/sudo
- 💾 **Backup**: Backup konfigurasi jaringan sebelum deployment
- 🧪 **Testing**: Test di lab environment sebelum production
- 🔧 **Customization**: Sesuaikan parameter sesuai network requirements
- 📚 **Learning**: Gunakan sebagai reference untuk networking automation

---

<div align="center">

**Made with Brain🧠 by Bagas Maulana**

⭐ **Star this repo** if you found it helpful!

[⬆️ Back to Top](#pc-router-ansible-playbook)

</div>

