# Panduan Navigasi Dasar Terminal Linux
---

## 📂 Struktur Direktori Linux (Hierarkis)

### `/` – Root Directory

* **Direktori paling atas** dalam sistem Linux.
* Semua file dan folder lain bercabang dari sini.
* Hanya **superuser (root)** yang biasanya bisa menulis langsung ke sini.

### `/home`

* Menyimpan **direktori pribadi setiap user**.
* Contoh: `/home/andi`, `/home/budi`.
* Berisi dokumen, konfigurasi pribadi, file download, dan workspace user.
* User root biasanya punya home sendiri: `/root`.

### `/etc`

* **Configuration files** untuk seluruh sistem.
* Contoh:

  * `/etc/passwd` → informasi user.
  * `/etc/hostname` → nama host komputer.
  * `/etc/network/` → konfigurasi jaringan.
* Isinya biasanya berupa file teks yang bisa diedit dengan editor seperti `nano` atau `vim`.

### `/bin`

* **Essential binaries** → program dasar yang dibutuhkan sistem agar bisa berjalan bahkan dalam mode pemulihan (rescue mode).
* Contoh: `ls`, `cp`, `mv`, `rm`, `cat`, `bash`.
* Dulu `/bin` hanya untuk root, tapi sejak Linux modern digabung dengan `/usr/bin`.

### `/usr`

* **Unix System Resources**, berisi aplikasi, library, dan data untuk user.
* Struktur dalam `/usr` juga punya sub-folder penting:

  * `/usr/bin` → program aplikasi user (misalnya `python3`, `gcc`, `firefox`).
  * `/usr/lib` → library pendukung aplikasi.
  * `/usr/share` → data umum (ikon, manual, locale).
  * `/usr/local` → tempat instalasi software manual (misalnya hasil `make install`).

### `/var`

* **Variable files** → file yang sering berubah ukurannya.
* Contoh:

  * `/var/log` → log sistem (syslog, dmesg, auth.log).
  * `/var/cache` → cache aplikasi.
  * `/var/spool` → antrian mail, print jobs, dll.
* Penting untuk troubleshooting karena log sistem ada di sini.


### `/tmp`

* **Temporary files** → digunakan oleh sistem atau aplikasi.
* Semua file di sini bisa hilang saat reboot.
* User bisa taruh file sementara di sini, tapi tidak untuk data penting.


### `/opt`

* **Optional software packages** → aplikasi tambahan dari vendor.
* Misalnya software komersial atau custom yang tidak masuk repo resmi Linux.
* Biasanya format: `/opt/nama-aplikasi/`.

### `/boot`

* Berisi file yang dibutuhkan saat **booting Linux**.
* Contoh: kernel (`vmlinuz`), initrd (initial ramdisk), dan konfigurasi GRUB (`grub.cfg`).
* Tanpa direktori ini, sistem tidak bisa start.

### 📌 Direktori penting lain (tambahan)

* `/sbin` → sistem binaries (untuk administrator, contoh `fdisk`, `iptables`).
* `/dev` → device files, representasi hardware (misalnya `/dev/sda` untuk harddisk, `/dev/tty` untuk terminal).
* `/proc` → virtual filesystem, berisi info kernel & proses (contoh: `/proc/cpuinfo`, `/proc/meminfo`).
* `/sys` → interface modern untuk kernel, mirip `/proc`.
* `/mnt` → mount point sementara (misalnya saat mount flashdisk manual).
* `/media` → mount point otomatis untuk media (USB, DVD, dll).
* `/lib` → library dasar sistem (pendukung `/bin` dan `/sbin`).

📖 Jadi, Linux memisahkan fungsi direktori agar:

* **Rapi** (konfigurasi di `/etc`, data user di `/home`).
* **Stabil** (program inti di `/bin`, library di `/lib`).
* **Mudah maintenance** (log di `/var`, boot loader di `/boot`).

--
