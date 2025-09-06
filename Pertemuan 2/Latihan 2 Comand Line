# Panduan Navigasi Dasar Terminal Linux

## Struktur Direktori Linux

Linux menggunakan struktur direktori hierarkis yang dimulai dari root directory (`/`). Berikut adalah direktori-direktori penting:

- **`/`** - Root directory, direktori utama dari semua direktori lainnya
- **`/home`** - Direktori home untuk semua user (kecuali root)
- **`/etc`** - File konfigurasi sistem
- **`/bin`** - Program/binary yang dapat dieksekusi
- **`/usr`** - Program dan data user
- **`/var`** - File yang sering berubah (log, cache, dll)
- **`/tmp`** - File temporary
- **`/opt`** - Software tambahan
- **`/boot`** - File untuk boot sistem

## Perintah Navigasi Dasar

### Melihat Lokasi dan Isi Direktori

```bash
# Melihat posisi direktori saat ini
pwd

# List file dan folder
ls                    # List basic
ls -l                 # List detail (permission, size, date)
ls -a                 # List semua file (termasuk hidden files)
ls -la                # Kombinasi detail + hidden files
ls -lh                # List dengan ukuran yang mudah dibaca (human readable)
ls -R                 # List recursive (semua subdirektori)

# Melihat struktur direktori dalam bentuk tree
tree                  # Perlu install: sudo apt install tree
```

### Berpindah Direktori

```bash
# Pindah ke direktori tertentu
cd /path/to/directory

# Shortcut penting
cd                    # Kembali ke home directory
cd ~                  # Kembali ke home directory
cd ..                 # Naik satu level ke parent directory
cd ../..              # Naik dua level
cd -                  # Kembali ke direktori sebelumnya
```

### Manipulasi File dan Direktori

```bash
# Membuat direktori
mkdir nama_folder
mkdir -p path/to/nested/folders    # Buat direktori nested sekaligus

# Menghapus direktori
rmdir nama_folder                  # Hapus direktori kosong
rm -r nama_folder                  # Hapus direktori beserta isinya
rm -rf nama_folder                 # Force delete (hati-hati!)

# Copy file/direktori
cp file1 file2                     # Copy file
cp file1 /path/to/destination/     # Copy ke direktori lain
cp -r folder1 folder2              # Copy direktori beserta isinya

# Move/rename file/direktori
mv file1 file2                     # Rename file
mv file1 /path/to/destination/     # Move file
mv folder1 /path/to/destination/   # Move direktori

# Hapus file
rm filename
rm -i filename                     # Konfirmasi sebelum hapus
rm *.txt                          # Hapus semua file .txt
```

## Perintah Membaca dan Edit File

### Membaca File

```bash
# Menampilkan isi file
cat filename                       # Tampilkan seluruh isi file
cat file1 file2                   # Tampilkan isi beberapa file sekaligus

# Membaca file besar
less filename                      # Navigasi dengan arrow keys, q untuk keluar
more filename                      # Mirip less tapi lebih basic

# Melihat bagian tertentu dari file
head filename                      # 10 baris pertama
head -n 20 filename               # 20 baris pertama
tail filename                      # 10 baris terakhir
tail -n 20 filename               # 20 baris terakhir
tail -f filename                   # Follow file (berguna untuk log files)
```

### Edit File

```bash
# Text editor
nano filename                      # Editor sederhana dan user-friendly
vim filename                       # Editor powerful tapi learning curve tinggi
gedit filename                     # GUI text editor (jika tersedia)
```

## Perintah Pencarian dan Informasi

```bash
# Mencari file
find /path -name "filename"        # Cari file berdasarkan nama
find . -name "*.txt"              # Cari semua file .txt di direktori current
find /home -type d -name "folder*" # Cari direktori yang namanya diawali "folder"

# Mencari dalam isi file
grep "text" filename               # Cari text dalam file
grep -r "text" /path              # Cari text dalam semua file di direktori
grep -i "text" filename           # Case insensitive search

# Informasi file
file filename                      # Melihat tipe file
stat filename                      # Informasi detail file
du -h filename                     # Ukuran file/direktori (human readable)
df -h                             # Melihat penggunaan disk
```

## Perintah Sistem dan Proses

```bash
# Informasi sistem
whoami                            # Nama user saat ini
id                                # User ID dan group ID
uname -a                          # Informasi sistem
uptime                            # Berapa lama sistem berjalan
date                              # Tanggal dan waktu

# Proses
ps                                # Lihat proses yang berjalan
ps aux                            # Lihat semua proses detail
top                               # Monitor proses real-time (q untuk keluar)
htop                              # Top yang lebih bagus (perlu install)
kill PID                          # Matikan proses berdasarkan PID
killall nama_program              # Matikan semua proses dengan nama tertentu
```

## Perintah Permission dan Ownership

```bash
# Melihat permission
ls -l filename                     # Lihat permission file

# Mengubah permission
chmod 755 filename                 # Numeric mode
chmod u+x filename                 # Symbolic mode (user execute)
chmod +x filename                  # Beri execute permission untuk semua

# Mengubah ownership
sudo chown user:group filename     # Ubah owner dan group
sudo chown -R user:group folder/   # Recursive untuk direktori
```

## Shortcut dan Tips Terminal

### Keyboard Shortcuts

```bash
Ctrl + C                          # Hentikan perintah yang sedang berjalan
Ctrl + Z                          # Suspend proses (fg untuk resume)
Ctrl + D                          # Exit/logout
Ctrl + L                          # Clear screen (sama dengan command 'clear')
Ctrl + R                          # Reverse search dalam history
Ctrl + A                          # Pindah ke awal baris
Ctrl + E                          # Pindah ke akhir baris
Ctrl + U                          # Hapus dari cursor ke awal baris
Ctrl + K                          # Hapus dari cursor ke akhir baris
```

### Tab Completion dan Wildcards

```bash
# Tab completion
cd /ho[TAB]                       # Auto complete menjadi /home/
ls Do[TAB]                        # Auto complete nama file/folder

# Wildcards
ls *.txt                          # Semua file dengan ekstensi .txt
ls file?.txt                      # file1.txt, file2.txt, dll (? = 1 karakter)
ls file[1-3].txt                  # file1.txt, file2.txt, file3.txt
```

### History Commands

```bash
history                           # Lihat history perintah
!!                                # Jalankan perintah terakhir
!n                                # Jalankan perintah ke-n dalam history
!string                           # Jalankan perintah terakhir yang diawali 'string'
```

## Perintah Network dan Transfer File

```bash
# Network
ping google.com                   # Test koneksi
wget URL                          # Download file dari internet
curl URL                          # Transfer data dari/ke server

# Transfer file
scp file user@host:/path          # Copy file via SSH
rsync -av source/ destination/    # Sync direktori
```

## Perintah Archive dan Compression

```bash
# Tar (archive)
tar -cvf archive.tar files/       # Buat archive
tar -xvf archive.tar              # Extract archive
tar -czvf archive.tar.gz files/   # Buat compressed archive (gzip)
tar -xzvf archive.tar.gz          # Extract compressed archive

# Zip
zip -r archive.zip folder/        # Buat zip archive
unzip archive.zip                 # Extract zip archive
```

## Tips Pembelajaran

1. **Mulai dari yang sederhana**: Gunakan `pwd`, `ls`, dan `cd` terlebih dahulu
2. **Gunakan `man` command**: `man ls` untuk melihat manual lengkap perintah `ls`
3. **Practice makes perfect**: Latih perintah-perintah ini secara rutin
4. **Gunakan `--help`**: Kebanyakan perintah punya opsi `--help` untuk bantuan cepat
5. **Hati-hati dengan `rm`**: Selalu double-check sebelum menghapus file, terutama dengan opsi `-rf`

## Perintah Help dan Documentation

```bash
man command                       # Manual lengkap untuk command
command --help                    # Bantuan singkat
info command                      # Info pages (alternatif man)
which command                     # Lokasi binary command
type command                      # Informasi tentang command
```

---

**Catatan**: Selalu berhati-hati ketika menggunakan perintah yang bisa menghapus atau mengubah file sistem. Gunakan opsi seperti `-i` (interactive) untuk konfirmasi sebelum melakukan operasi yang berisiko.
