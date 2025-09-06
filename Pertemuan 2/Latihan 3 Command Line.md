# Belajar Linux – Panduan Lengkap (Comprehensive Linux Guide)

> **Tujuan:** Panduan lengkap dan komprehensif perintah Linux mulai dari dasar hingga advanced. Mencakup shell, manajemen file, sistem administrasi, networking, scripting, dan troubleshooting. Cocok untuk pemula hingga intermediate. Semua contoh diasumsikan di distro keluarga Debian/Ubuntu kecuali disebut lain. Jalankan `man <perintah>` untuk detail lengkap.

---

## Daftar Isi
### Dasar
1. [Shell & Command Line Dasar](#shell--command-line-dasar)
2. [Sistem File & Navigasi](#sistem-file--navigasi)
3. [Perintah File & Direktori](#perintah-file--direktori)
4. [Melihat & Membaca File](#melihat--membaca-file)
5. [Text Processing & Editing](#text-processing--editing)
6. [Riwayat & Bantuan](#riwayat--bantuan)
7. [Menyalin, Memindah, Membuat, Menghapus](#menyalin-memindah-membuat-menghapus)
8. [Pencarian](#pencarian)
9. [Alias & Keluar](#alias--keluar)

### Sistem Administrasi
10. [Manajemen Paket & Distribusi Software](#manajemen-paket--distribusi-software)
11. [Arsip: tar & gzip](#arsip-tar--gzip)
12. [Dependensi Paket](#dependensi-paket)
13. [rpm, dpkg, yum, apt](#rpm-dpkg-yum-apt)
14. [Kompilasi dari Source Code](#kompilasi-dari-source-code)
15. [Pengguna & Grup](#pengguna--grup)
16. [Berkas Sistem: /etc/passwd, /etc/shadow, /etc/group](#berkas-sistem-etcpasswd-etcshadow-etcgroup)
17. [Permission, Ownership, umask, setuid/setgid, sticky](#permission-ownership-umask-setuidsetgid-sticky)
18. [/proc & Proses: ps, sinyal, kill, niceness, job control](#proc--proses-ps-sinyal-kill-niceness-job-control)

### Advanced
19. [Networking & Konektivitas](#networking--konektivitas)
20. [Monitoring & Performance](#monitoring--performance)
21. [Scripting & Automation](#scripting--automation)
22. [Environment Variables & PATH](#environment-variables--path)
23. [Disk Management & Filesystem](#disk-management--filesystem)
24. [Backup & Recovery](#backup--recovery)
25. [Security & Hardening](#security--hardening)
26. [Logs & Troubleshooting](#logs--troubleshooting)
27. [Systemd Services](#systemd-services)
28. [Cron & Scheduling](#cron--scheduling)

---

## Shell & Command Line Dasar

### Apa itu Shell?
**Shell** adalah program yang berfungsi sebagai interface antara user dan kernel Linux. Shell menerima perintah dari user, menerjemahkannya, dan mengeksekusinya.

#### Jenis-jenis Shell:
- **Bash** (Bourne Again Shell) - default di banyak distro
- **Zsh** (Z Shell) - fitur advanced, auto-completion yang bagus
- **Fish** (Friendly Interactive Shell) - user-friendly
- **Dash** - lightweight, cepat untuk scripting

```bash
# Lihat shell yang tersedia
cat /etc/shells

# Lihat shell yang sedang digunakan
echo $SHELL
ps -p $$

# Ganti shell default
chsh -s /bin/zsh
```

### Prompt dan Customization
```bash
# Lihat konfigurasi prompt saat ini
echo $PS1

# Contoh kustomisasi prompt di ~/.bashrc
export PS1='\[\e[32m\]\u@\h:\[\e[34m\]\w\[\e[0m\]\$ '

# Reload konfigurasi
source ~/.bashrc
```

## Sistem File & Navigasi

### Path: Relatif vs Absolut
- **Absolute path**: Dimulai dari root (`/`), contoh: `/home/user/documents`
- **Relative path**: Relatif terhadap direktori saat ini, contoh: `../documents`

```bash
# Lihat di mana kita berada
pwd                          # Print Working Directory

# Navigasi direktori
cd /path/tujuan              # ke direktori absolut
cd ..                        # naik 1 level
cd -                         # kembali ke direktori sebelumnya
cd                           # ke $HOME (sama dengan cd ~)
cd ~user                     # ke home directory user lain

# Shortcuts penting
cd /                         # ke root directory
cd ~                         # ke home directory
cd -                         # toggle between current and previous directory
```

### Listing dan Informasi File
```bash
# Basic listing
ls                           # list files dan directories
ls -l                        # long format (permissions, owner, size, date)
ls -a                        # termasuk hidden files (yang dimulai dengan .)
ls -la                       # kombinasi long format dan hidden files
ls -lh                       # human readable file sizes
ls -lt                       # sort by modification time (newest first)
ls -lr                       # reverse order
ls -lS                       # sort by file size
ls -R                        # recursive listing
ls -1                        # one file per line

# Advanced listing
ls -la --color=always        # force color output
ls -la --time-style=iso      # ISO timestamp format
ls -la --group-directories-first  # directories first
```

## Perintah File & Direktori

### Membuat File dan Direktori
```bash
# Membuat file
touch file.txt               # buat file kosong atau update timestamp
touch file1.txt file2.txt    # multiple files
touch "file dengan spasi.txt" # file dengan spasi

# Membuat direktori
mkdir proyek                 # buat direktori
mkdir -p a/b/c               # buat direktori bertingkat (parent directories)
mkdir -p project/{src,docs,tests}  # buat multiple directories sekaligus

# Template directory structure
mkdir -p myproject/{src/{main,test},docs,config,scripts}
```

### Informasi File
```bash
# Deteksi tipe file
file sesuatu.bin             # identifikasi tipe file
file *                       # check semua file di directory current

# Informasi detail file
stat file.txt                # informasi lengkap file (size, permissions, timestamps)
du -h file.txt              # disk usage
du -sh directory/           # total size directory
```

## Melihat & Membaca File

### Viewing File Contents
```bash
# Untuk file kecil
cat file.txt                 # tampilkan seluruh isi file
cat file1.txt file2.txt      # gabungkan multiple files
cat -n file.txt              # dengan nomor baris
cat -A file.txt              # tampilkan karakter khusus (tabs, newlines)

# Untuk file besar
less file.log                # viewer interaktif (q=keluar, /cari, n=next, b=back)
more file.log                # viewer sederhana (space=next page, q=quit)

# Bagian tertentu file
head file.txt                # 10 baris pertama (default)
head -n 20 file.txt          # 20 baris pertama
head -c 100 file.txt         # 100 karakter pertama

tail file.txt                # 10 baris terakhir (default)
tail -n 50 file.txt          # 50 baris terakhir
tail -f app.log              # follow log realtime (sangat berguna untuk monitoring)
tail -F app.log              # follow dengan retry jika file di-recreate

# Kombinasi head dan tail
sed -n '10,20p' file.txt     # baris 10-20
awk 'NR>=10 && NR<=20' file.txt  # alternative untuk range tertentu
```

### Navigasi dalam less/more
```bash
# Dalam less:
# j atau ↓      - turun satu baris
# k atau ↑      - naik satu baris
# Space atau f  - halaman berikutnya
# b             - halaman sebelumnya
# /pattern      - cari pattern ke depan
# ?pattern      - cari pattern ke belakang
# n             - repeat last search forward
# N             - repeat last search backward
# g             - ke awal file
# G             - ke akhir file
# q             - keluar
```

## Text Processing & Editing

### Text Editors
```bash
# Editor command line
nano file.txt               # editor sederhana, user-friendly
vim file.txt                # editor powerful, learning curve steep
emacs file.txt              # editor dengan banyak fitur

# Quick editing dengan sed
sed 's/old/new/g' file.txt  # replace all 'old' dengan 'new' (tampilkan saja)
sed -i 's/old/new/g' file.txt  # replace in-place (edit file)
sed -n '5,10p' file.txt     # print lines 5-10
```

### Text Processing Tools
```bash
# Sorting dan uniqueness
sort file.txt               # sort lines alphabetically
sort -n numbers.txt         # numeric sort
sort -r file.txt            # reverse sort
sort -k2 file.txt           # sort by second field
uniq file.txt               # remove duplicate adjacent lines
sort file.txt | uniq        # remove all duplicates
sort file.txt | uniq -c     # count occurrences

# Filtering dan processing
cut -d',' -f1,3 data.csv    # extract field 1 dan 3 (comma separated)
cut -c1-10 file.txt         # extract characters 1-10
awk '{print $1}' file.txt   # print first field (space separated)
awk -F',' '{print $2}' data.csv  # comma separated, print field 2
tr 'a-z' 'A-Z' < file.txt   # convert lowercase to uppercase
tr -d ' ' < file.txt        # remove all spaces

# Counting
wc file.txt                 # word count (lines, words, characters)
wc -l file.txt              # count lines only
wc -w file.txt              # count words only
wc -c file.txt              # count characters only
```

### Advanced Text Processing
```bash
# Using awk for complex processing
awk '{sum += $1} END {print sum}' numbers.txt  # sum first column
awk 'length > 80' file.txt  # lines longer than 80 characters
awk '/pattern/ {print NR ": " $0}' file.txt    # print line numbers for matches

# Using sed for advanced editing
sed '5d' file.txt           # delete line 5
sed '2,5d' file.txt         # delete lines 2-5
sed '/pattern/d' file.txt   # delete lines containing pattern
sed '2i\New line' file.txt  # insert line after line 2
```

## Riwayat & Bantuan
```bash
history                  # daftar riwayat perintah
!42                      # jalankan entri ke-42 di history
man ls                   # manual halaman ls
whatis ls                # ringkasan 1 baris
help cd                  # bantuan built-in shell
```

## Menyalin, Memindah, Membuat, Menghapus

### Operasi Copy (cp)
```bash
# Basic copying
cp src.txt dst.txt           # copy file
cp file.txt backup.txt       # create backup
cp -r dir1 dir2              # copy directory recursively
cp -a dir1 dir2              # archive mode (preserve all attributes)

# Advanced copying options
cp -i file.txt backup.txt    # interactive (ask before overwrite)
cp -u newer.txt older.txt    # update only if source is newer
cp -v file.txt backup.txt    # verbose (show what's being copied)
cp -p file.txt backup.txt    # preserve timestamps, ownership, permissions

# Multiple files
cp file1.txt file2.txt file3.txt destination_dir/
cp *.txt backup_dir/         # copy all .txt files
cp -r src/ dest/             # copy entire directory structure
```

### Operasi Move/Rename (mv)
```bash
# Rename dan move
mv old_name.txt new_name.txt # rename file
mv file.txt ../              # move to parent directory
mv dir1 dir2                 # rename directory
mv *.log logs/               # move all .log files to logs directory

# Options untuk mv
mv -i file.txt destination/  # interactive mode
mv -v file.txt destination/  # verbose mode
mv -u file.txt destination/  # update only if newer
```

### Operasi Delete (rm)
```bash
# Delete files
rm file.txt                  # delete single file
rm file1.txt file2.txt       # delete multiple files
rm *.tmp                     # delete all .tmp files

# Delete directories
rm -r old_dir                # remove directory and contents
rm -rf node_modules          # force remove (HATI-HATI!)
rm -i *.txt                  # interactive deletion

# Safer deletion (using trash)
# Install trash-cli: sudo apt install trash-cli
trash file.txt               # move to trash instead of permanent delete
trash-list                   # list items in trash
trash-restore               # restore from trash
trash-empty                 # empty trash
```

### Create Links
```bash
# Symbolic links (shortcuts)
ln -s /path/to/original /path/to/link    # create symbolic link
ln -sf /new/path /existing/link          # force update symbolic link
readlink /path/to/link                   # show where symlink points

# Hard links
ln /path/to/original /path/to/hardlink   # create hard link
```

### Bulk Operations
```bash
# Rename multiple files
rename 's/old/new/' *.txt    # rename using perl regex
mmv "*.txt" "backup_#1.txt"  # rename with pattern

# Copy with progress (for large files)
rsync -av --progress source/ destination/
pv large_file | cp /dev/stdin destination/  # with progress bar
```

## Pencarian

### Find - Mencari File dan Directory
```bash
# Pencarian berdasarkan nama
find . -name "*.log"                     # cari file .log dari current directory
find /var -name "*.conf"                 # cari file .conf di /var
find . -iname "*.TXT"                    # case-insensitive search
find . -name "test*" -type f             # hanya file (bukan directory)
find . -name "backup*" -type d           # hanya directory

# Pencarian berdasarkan atribut
find /var -type f -size +100M            # file lebih besar dari 100MB
find . -type f -size -1k                 # file lebih kecil dari 1KB
find . -type f -empty                    # file kosong
find . -type d -empty                    # directory kosong

# Pencarian berdasarkan waktu
find . -mtime -2                         # dimodifikasi dalam 2 hari terakhir
find . -mtime +7                         # dimodifikasi lebih dari 7 hari lalu
find . -atime -1                         # diakses dalam 1 hari terakhir
find . -ctime +30                        # status berubah lebih dari 30 hari lalu

# Pencarian berdasarkan permission
find . -perm 755                         # file dengan permission 755
find . -perm -644                        # file dengan minimal permission 644
find . -perm /u+w                        # file yang writable oleh owner

# Pencarian berdasarkan ownership
find . -user john                        # file milik user john
find . -group developers                 # file milik group developers
find . -uid 1000                         # file dengan UID 1000

# Aksi setelah menemukan
find . -name "*.tmp" -delete             # hapus semua file .tmp
find . -name "*.log" -exec rm {} \;      # hapus menggunakan exec
find . -name "*.txt" -exec cp {} backup/ \;  # copy ke backup directory
find . -type f -exec chmod 644 {} \;     # ubah permission semua file
find . -name "*.sh" -exec chmod +x {} \; # make executable

# Advanced find
find . -name "*.c" -o -name "*.h"        # OR condition (.c atau .h files)
find . \( -name "*.txt" -o -name "*.log" \) -size +1M  # complex conditions
find . -path "*/cache/*" -prune -o -name "*.py" -print  # exclude cache directories
```

### Grep - Pencarian dalam File
```bash
# Basic grep
grep "pattern" file.txt                  # cari pattern dalam file
grep -i "error" log.txt                  # case-insensitive
grep -v "debug" log.txt                  # invert match (exclude lines with 'debug')
grep -n "TODO" code.py                   # tampilkan nomor baris
grep -c "error" log.txt                  # count matches

# Multiple files
grep -r "function" src/                  # recursive search dalam directory
grep -R "error" /var/log/                # recursive dengan symbolic links
grep -l "main" *.c                       # list files yang mengandung pattern
grep -L "deprecated" *.py                # list files yang TIDAK mengandung pattern

# Advanced grep
grep -A 3 "error" log.txt                # show 3 lines after match
grep -B 2 "warning" log.txt              # show 2 lines before match
grep -C 5 "critical" log.txt             # show 5 lines around match
grep -w "word" file.txt                  # match whole words only
grep -x "exact line" file.txt            # match entire line

# Regular expressions
grep "^Start" file.txt                   # lines starting with 'Start'
grep "end$" file.txt                     # lines ending with 'end'
grep "[0-9]+" file.txt                   # lines containing numbers
grep -E "(error|warning|critical)" log.txt  # extended regex (OR)

# Useful grep combinations
ps aux | grep nginx                      # find nginx processes
history | grep ssh                       # find ssh commands in history
dmesg | grep -i error                    # find errors in kernel messages
```

### Locate - Fast File Search
```bash
# Update database dulu (sebagai root)
sudo updatedb

# Search menggunakan locate
locate filename                         # cari file berdasarkan nama
locate -i filename                      # case-insensitive
locate "*.pdf"                          # cari semua PDF files
locate -c "*.txt"                       # count hasil
locate -e filename                      # hanya tampilkan yang masih exist
locate -r "\.txt$"                      # menggunakan regex
```

### Which & Whereis - Mencari Program
```bash
# Mencari lokasi executable
which python3                           # lokasi executable python3
which -a python                         # semua lokasi python di PATH
whereis nginx                           # lokasi binary, source, manual nginx
type python                             # informasi tentang command
command -v git                          # portable way to find command
```

### Advanced Search Combinations
```bash
# Kombinasi find dan grep
find . -name "*.py" -exec grep -l "import os" {} \;  # Python files yang import os
find . -type f -exec grep -l "TODO" {} \; | head -10  # first 10 files with TODO

# Menggunakan xargs untuk efisiensi
find . -name "*.log" | xargs grep "error"           # lebih efisien untuk banyak file
find . -name "*.txt" -print0 | xargs -0 rm          # handle filenames dengan spasi

# Search dalam compressed files
zgrep "pattern" *.gz                     # grep dalam gzip files
bzgrep "pattern" *.bz2                   # grep dalam bzip2 files
```

## Alias & Keluar
```bash
alias ll='ls -lh --group-directories-first'
unalias ll
exit           # keluar dari shell
```

---

## Manajemen Paket & Distribusi Software
**Konsep:** Distribusi software di Linux dilakukan via **package** (berisi file binari, metadata, dependensi). Paket disimpan di **repository** (server resmi/third‑party).

- **Repository**: daftar sumber paket. Debian/Ubuntu di `/etc/apt/sources.list` dan `/etc/apt/sources.list.d/*.list`.
- **Cache metadata**: perlu `update` sebelum `install` agar daftar paket terbaru.
- **Upgrade**: memperbarui paket terpasang ke versi terbaru yang tersedia di repo.

```bash
# Debian/Ubuntu (APT)
sudo apt update                      # refresh metadata
sudo apt search htop                 # cari paket
sudo apt show htop                   # info detail
sudo apt install htop                # pasang
sudo apt remove htop                 # hapus (config tetap)
sudo apt purge htop                  # hapus + config
sudo apt upgrade                     # upgrade paket terpasang
sudo apt full-upgrade                # termasuk perubahan dependensi
```

### Menambahkan Repository Pihak Ketiga
```bash
# contoh: tambahkan kunci, lalu sumber repo .list
curl -fsSL https://example/key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/example.gpg
echo "deb [signed-by=/etc/apt/keyrings/example.gpg] https://repo.example stable main" | \
  sudo tee /etc/apt/sources.list.d/example.list
sudo apt update
```

## Arsip: tar & gzip
`tar` mengemas banyak file menjadi satu arsip; `gzip`/`bzip2`/`xz` mengompresinya.

```bash
# buat arsip
tar -cvf data.tar folder/
tar -czvf data.tar.gz folder/        # +gzip
tar -cJvf data.tar.xz folder/        # +xz

# ekstrak arsip
tar -xvf data.tar
tar -xzvf data.tar.gz
tar -xJvf data.tar.xz

# lihat isi tanpa ekstrak
tar -tf data.tar.gz
```

## Dependensi Paket
- Paket mendeklarasikan `Depends`, `Recommends`, `Suggests`.
- APT akan mengunduh dependensi otomatis saat install.
- Konflik/versi terblokir -> gunakan pinning/`apt-cache policy` untuk diagnosa.

```bash
apt-cache depends nginx
apt-cache rdepends libssl3
```

## rpm, dpkg, yum, apt
- **dpkg**: manajer tingkat rendah `.deb` (Debian/Ubuntu).
- **apt/apt-get**: antarmuka tingkat tinggi di atas dpkg.
- **rpm**: format paket Red Hat/Fedora/CentOS.
- **yum/dnf**: manajer paket tingkat tinggi di distro RPM.

```bash
# Debian/Ubuntu – pasang file .deb lokal
sudo dpkg -i paket.deb
sudo apt -f install       # perbaiki dependensi yang kurang

# RHEL/CentOS/Fedora
sudo rpm -i paket.rpm
sudo dnf install htop     # (pengganti yum)
```

## Kompilasi dari Source Code
Alur umum:
1. Pasang toolchain & dependency (`build-essential`, header dev).
2. Ambil source (`git clone`/tarball).
3. Konfigurasi build (`./configure` atau `cmake`).
4. Build (`make`/`cmake --build .`).
5. Install (`sudo make install`) – seringnya ke `/usr/local`.

```bash
# Debian/Ubuntu: toolchain
sudo apt update
sudo apt install build-essential pkg-config git

# Proyek berbasis Autotools
./configure --prefix=/usr/local
make -j$(nproc)
sudo make install

# Proyek berbasis CMake
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j
sudo cmake --install build
```

> **Tip:** gunakan `checkinstall`/paketisasi fpm untuk menghasilkan paket agar mudah di-uninstall.

---

## Pengguna & Grup
- **User** memiliki UID; **Group** memiliki GID; akses file ditentukan oleh owner, group, others.
- User biasa vs **root** (UID 0) sebagai superuser.
- Tambah/hapus user & grup memodifikasi entri di `/etc/passwd` dan `/etc/group` (via tool, **jangan** edit manual kecuali paham).

```bash
# tambah user (dengan home & shell bash)
sudo useradd -m -s /bin/bash alice
sudo passwd alice
# tambah ke grup tambahan (mis. sudo)
sudo usermod -aG sudo alice
# hapus user
sudo userdel -r alice

# grup
sudo groupadd devs
sudo usermod -aG devs alice
sudo gpasswd -d alice devs
```

## Berkas Sistem: /etc/passwd, /etc/shadow, /etc/group
- `/etc/passwd`: daftar user -> `nama:x:UID:GID:komentar:home:shell`
- `/etc/shadow`: hash password & kebijakan – **hanya root** yang dapat baca.
- `/etc/group`: daftar grup -> `nama:x:GID:anggota1,anggota2`

```bash
getent passwd alice
getent group devs
sudo chsh -s /bin/zsh alice     # ganti shell login
```

---

## Permission, Ownership, umask, setuid/setgid, sticky

### Permission Dasar
- **r (4)** baca, **w (2)** tulis, **x (1)** eksekusi.
- Berlaku untuk **user (u)** pemilik, **group (g)**, **others (o)**.

```bash
# tampilkan
ls -l
# ubah permission (simbolik & oktal)
chmod u+x script.sh
chmod g-w,o-r file.txt
chmod 750 bin/run.sh   # u=rwx (7), g=rx (5), o=--- (0)

# ubah kepemilikan
sudo chown alice:devs mydir -R
```

### umask
- Nilai default yang **mengurangi** permission saat file/direktori baru dibuat.
- Contoh umask `022` -> file baru biasanya `644`, dir baru `755`.

```bash
umask          # lihat nilai saat ini
umask 027      # lebih ketat: group r-x, others ---
```

### Setuid, Setgid, Sticky
- **setuid (s pada user)**: program berjalan dengan **UID pemilik** file (sering root). Contoh `passwd`. Oktal bit **4xxx**.
- **setgid (s pada group)**: mirip setuid untuk GID; pada **direktori**, file baru mewarisi grup direktori. Oktal bit **2xxx**.
- **sticky bit (t pada others)**: pada direktori bersama (mis. `/tmp`), hanya pemilik file yang boleh menghapusnya. Oktal bit **1xxx**.

```bash
# contoh
sudo chmod 4750 /usr/local/bin/mytool   # setuid + 750
sudo chmod 2755 /srv/shared              # setgid dir
sudo chmod 1777 /var/tmp                 # sticky dir umum
```

> ⚠️ **Keamanan:** setuid/setgid berisiko. Hindari memberi bit tersebut pada skrip atau binari tidak tepercaya.

---

## /proc & Proses: ps, sinyal, kill, niceness, job control

### Melihat Proses
```bash
ps aux | less                # snapshot semua proses
ps -ef                       # format lain
top                          # monitor interaktif
htop                         # (jika terpasang) lebih nyaman
```

### Detail Terminal & TTY
- Proses terikat ke **controlling terminal** (TTY). Lihat kolom `TTY` di `ps`.
- Proses daemon biasanya `?` (tanpa TTY).

### Pembuatan & Terminasi Proses
- Proses dibuat via `fork/exec` (shell mem-fork lalu menjalankan program).
- Terminasi normal (exit code 0) atau via **sinyal**.

### Sinyal & kill
```bash
kill -l                 # daftar sinyal
kill PID                # kirim SIGTERM (15) – minta berhenti sopan
kill -9 PID             # SIGKILL – paksa (tidak dapat ditangkap)
kill -HUP PID           # reload konfigurasi banyak daemon
pkill nginx             # berdasarkan nama
killall firefox         # semua proses bernama firefox
```

### Niceness & Prioritas
```bash
nice -n 10 long_task            # jalankan dengan nice 10 (lebih rendah prioritas)
renice -n -5 -p 12345           # tingkatkan prioritas PID 12345 (butuh sudo untuk negatif)
```

### Job Control (di shell)
```bash
cmd &                 # jalankan di background
jobs                  # daftar job
fg %1                 # bawa job #1 ke foreground
bg %1                 # lanjutkan di background
Ctrl+Z                # suspend (SIGTSTP)
disown                # lepaskan dari shell
```

### /proc Filesystem
- Pseudo‑filesystem yang memperlihatkan info kernel & proses.
- Contoh: `/proc/cpuinfo`, `/proc/meminfo`, `/proc/<PID>/fd`, `/proc/<PID>/status`.

```bash
cat /proc/meminfo | head
ls -l /proc/$$/fd     # fd milik shell saat ini
```


---

### Tips & Kebiasaan Baik
- Gunakan `sudo` seperlunya; hindari login langsung sebagai root.
- Tab‑completion & riwayat (`Ctrl+R`) mempercepat kerja.
- Simpan alias & export PATH di `~/.bashrc` atau `~/.zshrc`.
- Selalu baca `man`/`--help` sebelum menjalankan opsi berisiko (`rm -rf`, `chmod 7xxx`, dll.).
- Versi paket & perilaku dapat berbeda antar distro – sesuaikan perintah.

---

**Lisensi:** CC BY 4.0 – Silakan modifikasi untuk kebutuhan belajar/praktikum.

---

## Networking & Konektivitas

### Network Information
```bash
# Informasi network interface
ip addr show                         # tampilkan semua network interfaces
ip addr show eth0                    # informasi interface spesifik
ifconfig                             # legacy command (perlu net-tools)
ip route show                        # routing table
ip route add default via 192.168.1.1  # tambah default route

# Network connectivity
ping google.com                      # test connectivity
ping -c 4 8.8.8.8                   # ping 4 kali saja
traceroute google.com                # trace network path
mtr google.com                       # realtime ping + traceroute
nmap -sP 192.168.1.0/24             # scan network untuk active hosts
```

### Network Services & Ports
```bash
# Melihat koneksi network
netstat -tuln                        # listening ports (TCP/UDP)
netstat -tupln                       # dengan process ID
ss -tuln                             # modern replacement untuk netstat
ss -tulpn                            # dengan process info
lsof -i :80                          # process yang menggunakan port 80
lsof -i tcp:22                       # process pada port 22 TCP

# DNS lookup
nslookup google.com                  # DNS lookup
dig google.com                       # detailed DNS info
dig @8.8.8.8 google.com             # query specific DNS server
host google.com                     # simple hostname lookup
```

### File Transfer
```bash
# SCP - Secure Copy
scp file.txt user@server:/path/      # copy file ke remote server
scp user@server:/path/file.txt .     # copy dari remote server
scp -r directory/ user@server:/path/ # copy directory recursively
scp -P 2222 file.txt user@server:/path/  # custom SSH port

# Rsync - Advanced sync
rsync -av source/ destination/       # archive mode, verbose
rsync -av --progress src/ dest/      # dengan progress bar
rsync -av --delete src/ dest/        # delete files di destination yang tidak ada di source
rsync -av -e ssh src/ user@server:dest/  # over SSH

# wget & curl - Download dari internet
wget https://example.com/file.zip    # download file
wget -c https://example.com/big.iso  # resume download
curl -O https://example.com/file.zip # download dengan curl
curl -L https://example.com/redirect # follow redirects
```

---

## Monitoring & Performance

### System Information
```bash
# System overview
uname -a                             # kernel dan system info
uptime                               # system uptime dan load average
whoami                               # current user
who                                  # logged in users
w                                    # detailed user activity
id                                   # user dan group IDs
groups                               # user groups
```

### Resource Monitoring
```bash
# CPU dan Memory
top                                  # realtime process monitor
htop                                 # enhanced top (perlu install)
ps aux | head -20                    # snapshot processes
ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | head  # sort by memory usage

# Memory information
free -h                              # memory usage (human readable)
cat /proc/meminfo                    # detailed memory info
vmstat 2                             # virtual memory statistics every 2 seconds
sar -u 2 5                           # CPU usage (2 sec intervals, 5 times)

# Disk usage
df -h                                # disk space usage
du -sh */                            # directory sizes
du -sh * | sort -hr                  # sort by size (largest first)
lsblk                                # list block devices
```

### Performance Analysis
```bash
# I/O monitoring
iostat -x 2                          # I/O statistics
iotop                                # I/O usage by process (perlu install)
lsof +D /path/to/directory           # files opened in directory

# Network monitoring
iftop                                # network usage by connection
nethogs                             # network usage by process
tcpdump -i eth0                     # capture network packets
```

---

## Scripting & Automation

### Bash Scripting Basics
```bash
#!/bin/bash
# Shebang line - tells system which interpreter to use

# Variables
name="John"
age=25
echo "Hello, $name! You are $age years old."

# Command substitution
current_date=$(date)
file_count=`ls | wc -l`

# Conditional statements
if [ $age -gt 18 ]; then
    echo "Adult"
else
    echo "Minor"
fi

# Loops
for file in *.txt; do
    echo "Processing $file"
done

for i in {1..10}; do
    echo "Number: $i"
done

while read line; do
    echo "Line: $line"
done < input.txt

# Functions
function backup_file() {
    cp "$1" "$1.backup"
    echo "Backed up $1"
}

backup_file important.txt
```

### Script Examples
```bash
# System info script
#!/bin/bash
echo "=== System Information ==="
echo "Hostname: $(hostname)"
echo "Uptime: $(uptime -p)"
echo "Disk Usage:"
df -h | grep -v tmpfs
echo "Memory Usage:"
free -h
echo "Top 5 CPU processes:"
ps aux --sort=-%cpu | head -6

# Backup script
#!/bin/bash
SOURCE="/home/user/documents"
BACKUP="/backup/$(date +%Y%m%d)"
mkdir -p "$BACKUP"
rsync -av "$SOURCE/" "$BACKUP/"
echo "Backup completed to $BACKUP"

# Log monitoring script
#!/bin/bash
LOG_FILE="/var/log/syslog"
PATTERN="error|warning|critical"
tail -f "$LOG_FILE" | grep -i -E "$PATTERN" --color=always
```

---

## Environment Variables & PATH

### Working with Environment Variables
```bash
# Melihat environment variables
env                                  # semua environment variables
printenv                             # alternative command
echo $PATH                           # specific variable
echo $HOME                           # home directory
echo $USER                           # current user

# Setting variables
export MYVAR="Hello World"           # set dan export
export PATH=$PATH:/new/directory     # tambah ke PATH
unset MYVAR                          # hapus variable

# Persistent variables (tambah ke ~/.bashrc atau ~/.profile)
echo 'export JAVA_HOME=/usr/lib/jvm/java-11' >> ~/.bashrc
source ~/.bashrc                     # reload configuration
```

### PATH Management
```bash
# Melihat PATH
echo $PATH | tr ':' '\n'             # tampilkan PATH satu per baris

# Menambah directory ke PATH (temporary)
export PATH=$PATH:/opt/my-app/bin

# Menambah ke awal PATH (priority tinggi)
export PATH=/opt/my-app/bin:$PATH

# PATH permanent (tambah ke ~/.bashrc)
echo 'export PATH=$PATH:/opt/my-app/bin' >> ~/.bashrc
```

---

## Disk Management & Filesystem

### Disk Information
```bash
# Disk usage dan partitions
fdisk -l                             # list semua disk dan partitions
lsblk                                # tree view of block devices
blkid                                # UUID dan filesystem info
mount                                # mounted filesystems
df -T                                # filesystem types

# Disk space analysis
ncdu /                               # interactive disk usage analyzer
baobab                               # GUI disk usage analyzer (GNOME)
```

### Mount & Unmount
```bash
# Manual mounting
sudo mount /dev/sdb1 /mnt/usb        # mount USB drive
sudo mount -t ntfs /dev/sdb1 /mnt/windows  # mount Windows partition
sudo umount /mnt/usb                 # unmount

# Permanent mounting (edit /etc/fstab)
# UUID=xxx /mnt/storage ext4 defaults 0 2

# Network filesystems
sudo mount -t cifs //server/share /mnt/network -o username=user
sudo mount -t nfs server:/export /mnt/nfs
```

### Filesystem Operations
```bash
# Create filesystem
sudo mkfs.ext4 /dev/sdb1             # format sebagai ext4
sudo mkfs.ntfs /dev/sdb1             # format sebagai NTFS
sudo mkfs.fat -F32 /dev/sdb1         # format sebagai FAT32

# Check filesystem
sudo fsck /dev/sdb1                  # check dan repair filesystem
sudo e2fsck -f /dev/sdb1             # force check ext2/3/4
```

---

## Backup & Recovery

### Backup Strategies
```bash
# Full backup dengan tar
tar -czf backup-$(date +%Y%m%d).tar.gz /home/user/

# Incremental backup dengan rsync
rsync -av --link-dest=/backup/last /source/ /backup/current/
ln -sfn current /backup/last

# Database backup
mysqldump -u user -p database > backup.sql
pg_dump database > backup.sql

# System backup
sudo dd if=/dev/sda of=/backup/disk-image.img bs=4M status=progress
```

### Recovery Operations
```bash
# Restore dari tar
tar -xzf backup.tar.gz -C /restore/location/

# Restore dengan rsync
rsync -av /backup/current/ /restore/location/

# File recovery (undelete)
sudo testdisk                        # partition dan file recovery
sudo photorec                        # file recovery by type
```

---

## Security & Hardening

### User Security
```bash
# Password policy
chage -l username                    # lihat password info
sudo chage -M 90 username            # max password age 90 days
sudo passwd -e username              # force password change

# Account locking
sudo usermod -L username             # lock account
sudo usermod -U username             # unlock account
sudo faillock --user username --reset  # reset failed login attempts
```

### File Security
```bash
# File attributes
lsattr file.txt                      # lihat extended attributes
sudo chattr +i file.txt              # make immutable
sudo chattr -i file.txt              # remove immutable

# Access Control Lists (ACL)
getfacl file.txt                     # lihat ACL
setfacl -m u:alice:rw file.txt       # beri permission ke user alice
setfacl -x u:alice file.txt          # hapus ACL untuk alice
```

### System Security
```bash
# Firewall (UFW)
sudo ufw status                      # status firewall
sudo ufw enable                      # enable firewall
sudo ufw allow 22                    # allow SSH
sudo ufw allow from 192.168.1.0/24  # allow dari subnet specific
sudo ufw deny 80                     # block HTTP

# Process security
sudo ss -tulpn | grep :22            # check SSH service
sudo systemctl status ssh           # status SSH service
```

---

## Logs & Troubleshooting

### System Logs
```bash
# System logs (systemd)
journalctl                           # semua logs
journalctl -u ssh                    # logs untuk service ssh
journalctl -f                        # follow logs realtime
journalctl --since "2024-01-01"     # logs sejak tanggal tertentu
journalctl -p err                    # hanya error messages
journalctl --disk-usage              # disk usage oleh logs

# Traditional logs
tail -f /var/log/syslog              # system log
tail -f /var/log/auth.log            # authentication log
tail -f /var/log/kern.log            # kernel log
dmesg | tail                         # kernel ring buffer
```

### Troubleshooting Commands
```bash
# System state
systemctl status                     # system state
systemctl list-failed               # failed services
systemctl --failed                  # alternative
lscpu                                # CPU information
lsmem                                # memory information
lsusb                                # USB devices
lspci                                # PCI devices

# Network troubleshooting
ip link show                         # network interfaces
ping -c 4 8.8.8.8                   # test internet connectivity
nslookup google.com                  # test DNS resolution
telnet google.com 80                # test port connectivity
```

---

## Systemd Services

### Service Management
```bash
# Service status dan control
systemctl status nginx               # status service
systemctl start nginx                # start service
systemctl stop nginx                 # stop service
systemctl restart nginx              # restart service
systemctl reload nginx               # reload configuration
systemctl enable nginx               # enable at boot
systemctl disable nginx              # disable at boot

# Service information
systemctl list-units --type=service  # semua services
systemctl list-units --failed        # failed services
systemctl is-active nginx            # check if active
systemctl is-enabled nginx           # check if enabled at boot
```

### Creating Custom Services
```bash
# Create service file: /etc/systemd/system/myapp.service
[Unit]
Description=My Application
After=network.target

[Service]
Type=simple
User=myuser
WorkingDirectory=/opt/myapp
ExecStart=/opt/myapp/bin/myapp
Restart=always

[Install]
WantedBy=multi-user.target

# Reload dan start
sudo systemctl daemon-reload
sudo systemctl enable myapp
sudo systemctl start myapp
```

---

## Cron & Scheduling

### Crontab Management
```bash
# Edit crontab
crontab -e                           # edit user crontab
sudo crontab -e                      # edit root crontab
crontab -l                           # list current crontab
crontab -r                           # remove crontab

# Crontab format: minute hour day month weekday command
# 0 2 * * * /backup/script.sh        # setiap hari jam 2 pagi
# */15 * * * * /monitor/check.sh     # setiap 15 menit
# 0 0 1 * * /monthly/cleanup.sh      # setiap tanggal 1
```

### System-wide Cron
```bash
# System cron directories
ls /etc/cron.d/                      # additional cron files
ls /etc/cron.daily/                  # daily scripts
ls /etc/cron.weekly/                 # weekly scripts
ls /etc/cron.monthly/                # monthly scripts

# Anacron (untuk system yang tidak 24/7)
cat /etc/anacrontab                  # anacron configuration
```

### At Command (One-time scheduling)
```bash
# Schedule one-time tasks
echo "echo 'Hello' > /tmp/hello.txt" | at now + 5 minutes
at 14:30 tomorrow                    # interactive mode
atq                                  # list pending jobs
atrm 1                               # remove job #1
```

---

### Tips & Best Practices Lanjutan

#### Performance Optimization
```bash
# I/O scheduling
echo deadline | sudo tee /sys/block/sda/queue/scheduler

# CPU frequency scaling
cpufreq-info                         # CPU frequency info
echo performance | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
```

#### Security Best Practices
```bash
# SSH hardening
sudo nano /etc/ssh/sshd_config
# PermitRootLogin no
# PasswordAuthentication no
# AllowUsers specific_user

# File integrity monitoring
sudo apt install aide
sudo aide --init
sudo aide --check
```

#### System Maintenance
```bash
# Package cleanup
sudo apt autoremove                  # remove unused packages
sudo apt autoclean                   # clean package cache
sudo journalctl --vacuum-time=2weeks # clean old logs

# System updates
sudo apt update && sudo apt upgrade  # update system
sudo apt dist-upgrade                # major updates
```
