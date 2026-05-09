# PC Router Ansible Playbook

**Otomatisasi konfigurasi PC/Linux sebagai router dengan DHCP server menggunakan Ansible**

## 📋 Daftar Isi

- [Tentang Proyek](#tentang-proyek)
- [Fitur](#fitur)
- [Persyaratan](#persyaratan)
- [Instalasi](#instalasi)
- [Penggunaan](#penggunaan)
- [Konfigurasi](#konfigurasi)
- [Lisensi](#lisensi)
- [Kontributor](#kontributor)

## 📝 Tentang Proyek

Proyek ini menyediakan Ansible Playbook untuk mengkonfigurasi komputer berbasis Linux menjadi router dengan DHCP server. Playbook ini mengotomatisasi seluruh proses setup termasuk instalasi paket, konfigurasi network interface, pengaturan DHCP, dan konfigurasi iptables untuk Network Address Translation (NAT).

**Disclaimer**: Script ini dibuat 90% tanpa AI. AI hanya digunakan untuk membantu menjelaskan dokumentasi dari module Ansible.

## ✨ Fitur

- ✅ Instalasi otomatis ISC DHCP Server
- ✅ Konfigurasi interface jaringan sekunder
- ✅ Pengaturan dynamic DHCP pool dengan parameter interaktif
- ✅ Aktivasi IPv4 forwarding
- ✅ Konfigurasi NAT menggunakan iptables
- ✅ Setup berbasis template (Jinja2) untuk fleksibilitas
- ✅ Error handling yang baik

## 📦 Persyaratan

### Sistem Target
- **OS**: Linux berbasis Debian/Ubuntu (Ubuntu 18.04+, Debian 9+)
- **Akses**: Root atau sudo privileges
- **Network**: Minimal 2 interface jaringan (WAN dan LAN)

### Sistem Host (Ansible Controller)
- **Ansible**: v2.9+
- **Python**: 3.6+

### Paket yang akan diinstal
- `isc-dhcp-server` - DHCP server
- `iptables` - Firewall & NAT configuration

## 🚀 Instalasi

### 1. Clone Repository

```bash
git clone https://github.com/yourusername/ansible-pc-router.git
cd ansible-pc-router
```

### 2. Install Ansible (jika belum terinstal)

```bash
# Untuk Ubuntu/Debian
sudo apt-get update
sudo apt-get install ansible

# Atau menggunakan pip
pip install ansible
```

### 3. Setup Inventory

Buat file `inventory.ini` atau edit file inventory yang sudah ada:

```ini
[target]
your-router-ip ansible_user=your-username ansible_port=22
```

### 4. Persiapan SSH Access

Pastikan Anda dapat mengakses target machine via SSH:

```bash
ssh your-username@your-router-ip
```

## 💻 Penggunaan

### Menjalankan Playbook

```bash
ansible-playbook -i inventory.ini pc-router-ansible.yaml
```

### Dengan Verbose Output

```bash
ansible-playbook -i inventory.ini pc-router-ansible.yaml -vv
```

### Menjalankan pada Host Tertentu

```bash
ansible-playbook -i inventory.ini pc-router-ansible.yaml -l target-host
```

## ⚙️ Konfigurasi

Playbook ini akan meminta input interaktif untuk parameter berikut:

| Parameter | Contoh | Deskripsi |
|-----------|--------|-----------|
| `wan_interface` | `ens33` | Interface yang terhubung ke internet/publik |
| `secondary_interface` | `ens37` | Interface untuk LAN/distribusi |
| `secondary_ip_addr` | `100.100.10.1/24` | IP address interface LAN (CIDR format) |
| `dhcp_network` | `100.100.10.0` | Network address untuk DHCP pool |
| `dhcp_netmask` | `255.255.255.0` | Netmask untuk DHCP pool |
| `dhcp_range` | `100.100.10.2 100.100.10.254` | Range IP yang akan di-assign ke client |
| `dhcp_dns` | `8.8.8.8` | DNS server untuk client DHCP |
| `dhcp_gateway` | `100.100.10.1` | Gateway untuk client DHCP |
| `dhcp_broadcast` | `100.100.10.255` | Broadcast address |

### Contoh Input

```
Masukkan interface yang mengarah ke public (Contoh : ens33): ens33
Masukkan interface kedua anda (Contoh : ens37): ens37
Masukkan ip address interface kedua dengan prefix (Contoh : 100.100.10.1/24): 192.168.100.1/24
Masukkan network untuk dhcp pool anda (Contoh : 100.100.10.0): 192.168.100.0
Masukkan netmask untuk dhcp pool anda (Contoh : 255.255.255.0): 255.255.255.0
Masukkan range untuk dhcp pool anda (Contoh : 100.100.10.2 100.100.10.254): 192.168.100.10 192.168.100.200
Masukkan nameserver/DNS untuk dhcp pool anda (Contoh : 8.8.8.8): 8.8.8.8
Masukkan gateway untuk dhcp pool anda (Contoh : 100.100.10.1): 192.168.100.1
Masukkan broadcast address untuk dhcp pool anda (Contoh : 100.100.10.255): 192.168.100.255
```

## 📁 File Structure

```
ansible-pc-router/
├── README.md                 # File dokumentasi ini
├── pc-router-ansible.yaml    # Main playbook
├── dhcpd.conf               # Template konfigurasi DHCP
├── isc-dhcp-server          # Template default isc-dhcp-server
└── inventory.ini            # File inventory (optional)
```

## 🔧 Tasks yang Dijalankan

Playbook akan menjalankan tasks berikut secara berurutan:

1. **Menampilkan informasi creator** - Pause untuk menampilkan kredit
2. **Menampilkan disclaimer** - Informasi tentang penggunaan AI
3. **Update repository** - Update package cache
4. **Instalasi paket**:
   - ISC DHCP Server
   - iptables
5. **Konfigurasi network interface** - Menambahkan secondary interface ke `/etc/network/interfaces`
6. **Inject DHCP config** - Copy template `dhcpd.conf` dengan variabel yang sudah diisi
7. **Inject isc-dhcp-server defaults** - Copy template default isc-dhcp-server configuration
8. **Aktivasi IPv4 forwarding** - Enable packet forwarding
9. **Konfigurasi iptables NAT** - Setup MASQUERADE untuk WAN interface
10. **Restart DHCP service** - Restart isc-dhcp-server

## ✅ Verifikasi Setup

Setelah playbook selesai, verifikasi konfigurasi:

### Cek status DHCP Server
```bash
sudo systemctl status isc-dhcp-server
```

### Cek IP address
```bash
ip addr show
```

### Cek IPv4 forwarding
```bash
cat /proc/sys/net/ipv4/ip_forward
# Harus menampilkan 1
```

### Cek iptables rules
```bash
sudo iptables -t nat -L -n -v
```

### Test DHCP (dari client)
```bash
sudo dhclient ens37  # atau sesuai interface
```

## 🐛 Troubleshooting

### DHCP Server tidak berjalan
```bash
# Check logs
sudo journalctl -u isc-dhcp-server -n 50

# Verify config syntax
sudo dhcpd -t -cf /etc/dhcp/dhcpd.conf
```

### Interface tidak mendapat IP
```bash
# Restart networking
sudo systemctl restart networking

# Atau
sudo systemctl restart isc-dhcp-server
```

### Koneksi ke target machine gagal
```bash
# Test SSH connection
ssh -v your-username@your-router-ip

# Check Ansible inventory
ansible-inventory -i inventory.ini --list
```

## 📚 Referensi

- [ISC DHCP Server Documentation](https://www.isc.org/dhcp/)
- [Ansible Documentation](https://docs.ansible.com/)
- [Linux iptables Tutorial](https://www.netfilter.org/documentation/HOWTO/packet-filtering-HOWTO.html)
- [Network Configuration - Ubuntu](https://ubuntu.com/server/docs/network-configuration)

## 📜 Lisensi

[Tentukan lisensi yang sesuai - contoh: MIT, GPL, dll]

## 👤 Kontributor

**Original Creator**: Bagas Maulana  
**Institusi**: Universitas Bani Saleh

## 🤝 Kontribusi

Kontribusi sangat diterima! Silakan:
1. Fork repository ini
2. Buat branch fitur (`git checkout -b feature/AmazingFeature`)
3. Commit perubahan (`git commit -m 'Add some AmazingFeature'`)
4. Push ke branch (`git push origin feature/AmazingFeature`)
5. Buat Pull Request

## ⚠️ Catatan Penting

- Script ini memerlukan akses root/sudo pada target machine
- Pastikan backup konfigurasi jaringan sebelum menjalankan playbook
- Test di lingkungan lab terlebih dahulu sebelum production
- Sesuaikan parameter DHCP sesuai dengan kebutuhan network Anda

---

**Last Updated**: 2024  
**For issues or questions, please create an issue in the repository**
