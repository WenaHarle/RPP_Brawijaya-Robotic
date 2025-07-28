# 01 - Instalasi dan Konfigurasi WSL Ubuntu 22.04

## 🎯 **Tujuan**
Menginstall dan mengkonfigurasi Ubuntu 22.04 pada WSL2 dengan optimal.

## 📋 **Prasyarat**
- Windows 10 version 2004+ atau Windows 11
- Akses Administrator
- Koneksi internet

## 🚀 **Langkah Instalasi**

### 1. Enable WSL Feature

Buka **PowerShell sebagai Administrator** dan jalankan:

```powershell
# Enable WSL dan Virtual Machine Platform
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

# Restart komputer setelah perintah di atas
```

### 2. Download dan Install WSL2 Kernel Update

```powershell
# Download WSL2 kernel update (jalankan di PowerShell)
# Atau download manual dari: https://aka.ms/wsl2kernel
```

### 3. Set WSL2 sebagai Default

```powershell
wsl --set-default-version 2
```

### 4. Install Ubuntu 22.04

```powershell
# Lihat distro yang tersedia
wsl --list --online

# Install Ubuntu 22.04
wsl --install -d Ubuntu-22.04

# Atau install dari Microsoft Store
```

### 5. Setup Initial Configuration

Setelah instalasi, Ubuntu akan meminta setup user:

```bash
# Masukkan username (huruf kecil, tanpa spasi)
# Masukkan password (tidak akan terlihat saat diketik)
```

## ⚙️ **Konfigurasi Awal**

### 1. Update Sistem

```bash
# Update package list
sudo apt update

# Upgrade semua package
sudo apt upgrade -y

# Install package essential
sudo apt install -y curl wget git vim nano htop tree unzip
```

### 2. Konfigurasi Git (Opsional)

```bash
# Set username dan email untuk Git
git config --global user.name "Nama Anda"
git config --global user.email "email@anda.com"

# Verifikasi konfigurasi
git config --list
```

### 3. Konfigurasi WSL

Buat file konfigurasi WSL di Windows:

```powershell
# Buat file .wslconfig di %USERPROFILE%
notepad %USERPROFILE%\.wslconfig
```

Isi file `.wslconfig`:

```ini
[wsl2]
memory=4GB
processors=2
swap=2GB
```

## 🔧 **Perintah WSL Penting**

### Mengelola WSL dari Windows

```powershell
# Lihat semua distro yang terinstall
wsl --list --verbose

# Jalankan distro tertentu
wsl -d Ubuntu-22.04

# Shutdown WSL
wsl --shutdown

# Restart WSL
wsl --shutdown
wsl

# Set distro default
wsl --set-default Ubuntu-22.04

# Export distro (backup)
wsl --export Ubuntu-22.04 C:\backup\ubuntu-backup.tar

# Import distro (restore)
wsl --import Ubuntu-Backup C:\WSL\Ubuntu-Backup C:\backup\ubuntu-backup.tar
```

### Akses File System

```bash
# Akses drive Windows dari Ubuntu
ls /mnt/c/Users/

# Akses home Ubuntu dari Windows
# Di File Explorer: \\wsl$\Ubuntu-22.04\home\username\
```

## 🧪 **Testing Instalasi**

Verifikasi instalasi dengan perintah berikut:

```bash
# Cek versi Ubuntu
lsb_release -a

# Cek versi kernel
uname -a

# Cek WSL version
cat /proc/version

# Cek space disk
df -h

# Cek memory
free -h

# Cek informasi sistem
hostnamectl
```

## 🎨 **Kustomisasi Terminal**

### 1. Install Zsh (Opsional)

```bash
# Install Zsh
sudo apt install -y zsh

# Install Oh My Zsh
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Set Zsh sebagai default shell
chsh -s $(which zsh)
```

### 2. Kustomisasi Bash

```bash
# Edit .bashrc
nano ~/.bashrc

# Tambahkan alias berguna
echo 'alias ll="ls -alF"' >> ~/.bashrc
echo 'alias la="ls -A"' >> ~/.bashrc
echo 'alias l="ls -CF"' >> ~/.bashrc
echo 'alias cls="clear"' >> ~/.bashrc

# Reload .bashrc
source ~/.bashrc
```

## 🔍 **Troubleshooting Umum**

### WSL tidak bisa start

```powershell
# Restart WSL service
wsl --shutdown
wsl

# Atau restart LxssManager service
net stop LxssManager
net start LxssManager
```

### Error "WSL 2 requires an update to its kernel component"

```powershell
# Download dan install WSL2 kernel update
# https://aka.ms/wsl2kernel
```

### Performa lambat

```bash
# Pindah project ke filesystem Linux
mv /mnt/c/projects ~/projects

# Gunakan Windows Terminal untuk performa lebih baik
```

## 📝 **Best Practices**

1. **File Location**: Simpan project di filesystem Linux (`~/`) untuk performa optimal
2. **Windows Terminal**: Gunakan Windows Terminal untuk experience yang lebih baik
3. **Resource Management**: Set limit memory dan CPU di `.wslconfig`
4. **Regular Updates**: Update sistem secara rutin dengan `sudo apt update && sudo apt upgrade`
5. **Backup**: Buat backup distro secara berkala dengan `wsl --export`

## ✅ **Checklist Setup**

- [ ] WSL2 terinstall dan aktif
- [ ] Ubuntu 22.04 terinstall
- [ ] User account dibuat
- [ ] System di-update ke versi terbaru
- [ ] Git dikonfigurasi (jika diperlukan)
- [ ] Essential packages terinstall
- [ ] WSL configuration optimal
- [ ] Terminal dikustomisasi

## 🎯 **Latihan**

1. Coba akses file Windows dari Ubuntu
2. Buat folder project di home directory Ubuntu
3. Test performa dengan membuat file besar
4. Ekspor dan impor distro untuk latihan backup

---

**Selanjutnya: [02-Basic-Commands.md](02-Basic-Commands.md) - Perintah Dasar Linux**
