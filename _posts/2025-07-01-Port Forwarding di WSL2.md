---
title: "Port Forwarding di WSL2: Akses SSH dari Host dan Jaringan Lokal"
categories: WSL2
date: 2025-07-01
author: Ilham
tags: [wsl2, ssh, windows, port-forwarding, jaringan]
summary: "Panduan lengkap mengakses SSH di WSL2 dari Windows host dan komputer lain di jaringan lokal melalui port forwarding dan konfigurasi firewall."
image: /assets/images/wsl2-ssh-cover.png
key: 2025-07-01-WSL2-1
---

![WSL2 SSH](/assets/images/wsl2-ssh-cover.png "WSL2 SSH")

WSL2 (Windows Subsystem for Linux versi 2) memungkinkan kita menjalankan Linux secara native di Windows. Namun, secara default, WSL2 menggunakan virtual network dengan IP yang berubah-ubah dan tidak langsung bisa diakses dari komputer lain di jaringan lokal.

Di artikel ini, kita bakal kulik cara gampang agar WSL2 bisa diakses lewat **SSH**. Ini sangat berguna jika Anda ingin melakukan _remote development_ dari laptop lain, mengelola server kecil di WSL2 dari tablet, atau sekedar ingin memisahkan lingkungan kerja Anda. Kita akan lakukan dengan:

- **Set port forwarding di Windows** — supaya koneksi dari luar diarahkan ke WSL2.
- **Buka port di Windows Firewall** — supaya akses SSH-nya nggak terblokir.

Langsung aja kita mulai dari langkah pertama.

## 1. Instalasi dan Aktivasi SSH Server di WSL2

### Install SSH Server

Masuk ke terminal WSL2, lalu jalankan perintah berikut untuk update dan install SSH server:

```bash
sudo apt update && sudo apt install openssh-server
```

### Aktifkan SSH Server

Untuk memulai service SSH, jalankan:

```bash
sudo service ssh start
```

Cek status SSH:

```bash
sudo service ssh status
```

Jika statusnya `sshd is running.` atau `active (running)`, berarti SSH server sudah aktif. Jika tertulis `sshd is not running ... failed!` atau `inactive (dead)`, jalankan ulang `sudo service ssh start`.

**Beberapa perintah lain yang berguna:**

- `sudo service ssh stop` — Mematikan SSH server
- `sudo service ssh restart` — Me-restart service SSH

### (Opsional) Ganti Port Default

Edit file konfigurasi:

```bash
sudo nano /etc/ssh/sshd_config
```

Ubah:

```bash
#Port 22
```

Menjadi:

```bash
Port 2222
```

Simpan lalu restart SSH:

```bash
sudo service ssh restart
```

## 2. Cek IP WSL2

Jalankan perintah:

```bash
hostname -I
```

Contoh output:

```bash
172.22.xxx.xx
```

**Catatan:** IP ini bisa berubah setiap kali WSL2 direstart. Jadi, **perlu diperbarui juga di konfigurasi portproxy Windows jika berubah**.
{:.warning}

## 3. Konfigurasi Port Forwarding di Windows

Windows perlu meneruskan koneksi dari port tertentu (misalnya 2222) ke IP dan port SSH WSL2.

### Mengecek Portproxy yang Aktif

Buka PowerShell sebagai Administrator, karena perintah `netsh` dan `New-NetFirewallRule` memerlukan hak akses tinggi untuk mengubah konfigurasi jaringan dan keamanan di Windows.

```powershell
netsh interface portproxy show all
```

Contoh output:

```powershell
Listen on ipv4:             Connect to ipv4:
Address         Port        Address         Port
--------------- ----------  --------------- ----------
0.0.0.0         2222        172.22.134.101  2222
```

### Tambah Rule Port Forwarding

```powershell
netsh interface portproxy add v4tov4 listenport=2222 listenaddress=0.0.0.0 connectport=2222 connectaddress=172.22.xxx.xx
```

- `listenport`: Port di sisi Windows
- `listenaddress`: `0.0.0.0` agar bisa diakses dari komputer lain
- `connectaddress`: IP WSL2 (hasil dari `hostname -I`)
- `connectport`: Port SSH yang digunakan (22 atau 2222)

### (Opsional) Hapus Rule Port Forwarding

Karena IP nya tidak konsisten, mungkin kita perlu menambahkan rule ulang untuk port forwarding.
Sebelum menambahkan rule lagi alangkah baiknya kita hapus dulu rule yang sudah ada agar tidak menumpuk.

```powershell
netsh interface portproxy delete v4tov4 listenport=2222 listenaddress=0.0.0.0
```

## 4. Pengaturan Firewall Windows

Agar komputer lain bisa terhubung ke port forwarding tadi, kita perlu membuka port di firewall Windows.

### Mengecek rule firewall yang ada

```powershell
Get-NetFirewallRule | Where-Object { $_ | Get-NetFirewallPortFilter | Where-Object { $_.LocalPort -eq 2222 } }
```

Jika tidak ada hasil, berarti port belum dibuka.

- `Get-NetFirewallRule`: Mengambil semua rule firewall yang ada.
- `Where-Object { ... }`: Filter untuk mencari rule yang menggunakan **port 2222**.
- `Get-NetFirewallPortFilter`: Menampilkan properti port dari setiap rule.
- `LocalPort -eq 2222`: Hanya menampilkan rule yang menggunakan port 2222 secara lokal.

**Gunanya:** untuk memastikan apakah sudah ada rule firewall untuk port 2222 sebelum kita buat yang baru.

### Membuat rule firewall baru (membuka port 2222)

Jika belum dibuka jadi kita harus membuat rule firewall baru.

```powershell
New-NetFirewallRule -DisplayName "WSL2 SSH External" -Direction Inbound -LocalPort 2222 -Protocol TCP -Action Allow
```

- `New-NetFirewallRule`: Membuat rule firewall baru.
- `-DisplayName`: Nama yang ditampilkan untuk rule ini di Windows Firewall.
- `-Direction Inbound`: Rule ini untuk **lalu lintas masuk** ke komputer.
- `-LocalPort 2222`: Membuka **port 2222** agar bisa menerima koneksi masuk.
- `-Protocol TCP`: Rule ini berlaku untuk protokol **TCP**.
- `-Action Allow`: Mengizinkan koneksi yang cocok dengan rule ini.

**Gunanya:** Mengizinkan koneksi masuk ke port 2222 — port tempat WSL2 menerima SSH.

### Menonaktifkan rule firewall (tanpa menghapusnya)

```powershell
Set-NetFirewallRule -DisplayName "WSL2 SSH External" -Enabled False
```

- `Set-NetFirewallRule`: Mengubah properti dari rule firewall yang ada.
- `-DisplayName`: Menargetkan rule berdasarkan nama (harus sama persis).
- `-Enabled False`: Menonaktifkan rule tanpa menghapusnya.

**Gunanya:** Berguna jika kamu ingin **sementara memblokir akses** ke port 2222 tanpa kehilangan konfigurasi rule yang sudah dibuat.

### Mengaktifkan kembali rule yang sudah dinonaktifkan

```powershell
Set-NetFirewallRule -DisplayName "WSL2 SSH External" -Enabled True
```

- Sama seperti sebelumnya, tapi `-Enabled True` untuk mengaktifkan kembali rule yang sudah ada.

**Gunanya:** Mengaktifkan ulang akses port SSH jika sebelumnya kamu nonaktifkan dengan `Enabled False`.

### Menghapus rule firewall secara permanen

```powershell
Remove-NetFirewallRule -DisplayName "WSL2 SSH External"
```

- `Remove-NetFirewallRule`: Menghapus rule firewall berdasarkan nama.
- `-DisplayName`: Nama dari rule yang ingin dihapus.

**Gunanya:** Menghapus rule secara permanen dari Windows Firewall. Setelah ini, koneksi ke port 2222 akan ditolak kecuali kamu buat rule baru lagi.

### Melihat Semua Rule Firewall

Kalau ingin melihat semua rule firewall yang aktif atau nonaktif:

```powershell
Get-NetFirewallRule | Format-Table Name, DisplayName, Enabled, Direction, Action
```

## 5. Testing Koneksi SSH

### Dari Windows Host (localhost)

```bash
ssh user@localhost -p 2222
```

### Dari Komputer Lain di Jaringan yang sama (Lokal)

```bash
ssh user@192.168.x.x -p 2222
```

### Menggunakan PuTTY

- Hostname: `localhost` atau IP LAN Windows
- Port: `2222`
- Connection type: SSH

## 6. Troubleshooting

Tidak menutup kemungkinan jika kita akan mengalami error saat melakukan konfigurasi.
Jadi jika terdapat error seperti `kex_exchange_identification: read: Connection reset by peer` coba solusi dibawah ini.

**Solusi:**

- Pastikan IP WSL2 belum berubah → cek ulang `hostname -I`
- Restart SSH di WSL2:

```bash
sudo service ssh restart
```

- Restart _IP Helper Service_ di Windows:

```powershell
Restart-Service iphlpsvc
```

- Pastikan firewall tidak memblokir port 2222
- Nonaktifkan antivirus/VPN sementara untuk memastikan tidak ada yang menghalangi koneksi

## 7. Tips Tambahan

### Script Otomatis Port Forwarding (PowerShell)

Kamu bisa menggunakan skrip berikut untuk menyegarkan konfigurasi setiap kali IP WSL2 berubah:

```powershell
$wslIp = (wsl hostname -I).Trim()
netsh interface portproxy delete v4tov4 listenport=2222 listenaddress=0.0.0.0
netsh interface portproxy add v4tov4 listenport=2222 listenaddress=0.0.0.0 connectport=2222 connectaddress=$wslIp
```

**Opsional:** Tambahkan skrip ini ke **Task Scheduler** agar dijalankan otomatis saat login/startup.
{:.info}

## Kesimpulan

Dengan mengatur port forwarding dan membuka firewall di Windows, kamu bisa mengakses WSL2 via SSH dari komputer lain di jaringan lokal. Ini membuat WSL2 jauh lebih fleksibel dan bisa digunakan layaknya server Linux sungguhan.

Kalau kamu punya pertanyaan atau mengalami kendala saat menerapkan panduan ini, silakan tinggalkan komentar atau kirim pesan langsung. Terima kasih sudah membaca!
