### Latihan 1: Sistem Kontrol Robot

1. **Setup:**# 📚 Panduan Git untuk Programmer Robotik

## 🎯 Tujuan Pembelajaran

Setelah mempelajari materi ini, peserta akan mampu:

* ✅ Memahami konsep version control dan mengapa penting untuk proyek robotik
* ✅ Menggunakan Git untuk mengelola kode robot dengan percaya diri
* ✅ Memahami workflow Git dari dasar hingga kolaborasi tim robotik
* ✅ Mengatasi masalah umum yang sering terjadi dalam Git
* ✅ Bekerja dengan GitHub untuk kolaborasi dalam proyek robotik

---

## 🧠 Memahami Konsep Dasar Git

### Apa itu Version Control?

Bayangkan Anda sedang mengembangkan kode robot. Setiap kali mengubah algoritma, Anda menyimpan dengan nama berbeda:
- `robot_control_v1.py`
- `robot_control_v2.py`
- `robot_control_final.py`
- `robot_control_final_fixed.py`
- `robot_control_final_beneran_jalan.py`

**Git** melakukan hal yang sama, tapi jauh lebih pintar! Git mencatat:
- ✨ **Apa** yang berubah (baris mana yang ditambah/dihapus)
- ⏰ **Kapan** perubahan terjadi
- 👤 **Siapa** yang mengubah
- 💬 **Mengapa** diubah (melalui pesan commit)

### Konsep Penting dalam Git

| Istilah | Analogi Sederhana | Penjelasan |
|---------|-------------------|------------|
| **Repository (repo)** | Folder proyek yang "diawasi" | Tempat Git menyimpan semua history perubahan |
| **Commit** | Snapshot/foto kondisi proyek | Menyimpan kondisi file pada waktu tertentu |
| **Branch** | Jalur paralel | Versi berbeda dari proyek yang bisa dikembangkan bersamaan |
| **Merge** | Menggabungkan jalur | Menyatukan perubahan dari branch berbeda |
| **Remote** | Server penyimpanan online | Tempat menyimpan repository di internet (GitHub, GitLab) |

---

## 🛠️ Setup Awal Git

### Langkah 1: Cek Instalasi Git

```bash
git --version
```

**Output yang diharapkan:**
```
git version 2.34.1
```

### Langkah 2: Konfigurasi Identitas (WAJIB!)

```bash
# Atur nama Anda
git config --global user.name "John Doe"

# Atur email Anda
git config --global user.email "john.doe@email.com"

# Cek konfigurasi
git config --list
```

**💡 Mengapa penting?** Setiap commit akan mencatat siapa yang melakukan perubahan.

### Langkah 3: Konfigurasi Tambahan (Opsional)

```bash
# Atur editor default untuk menulis pesan commit
git config --global core.editor "code --wait"  # VS Code
# atau
git config --global core.editor "nano"         # Nano editor

# Atur branch default menjadi 'main'
git config --global init.defaultBranch main
```

---

## 🧪 Percobaan Git: Dari Nol hingga Mahir

### 🔬 Percobaan 1: Memahami Git Status

**Tujuan:** Memahami 3 area kerja Git

```bash
# Buat folder proyek robot
mkdir robot-arm-controller
cd robot-arm-controller

# Inisialisasi Git
git init
```

**Output:**
```
Initialized empty Git repository in /path/robot-arm-controller/.git/
```

**Cek status:**
```bash
git status
```

**Output:**
```
On branch main
No commits yet
nothing to commit (create/copy files and use "git add" to track)
```

**💡 Penjelasan:** Git memberi tahu kita bahwa:
- Kita di branch `main`
- Belum ada commit
- Belum ada file untuk dilacak

### 🔬 Percobaan 2: Memahami Working Directory, Staging, dan Repository

Mari kita buat file dan amati perubahannya:

```bash
# Buat file Python sederhana untuk kontrol motor
echo '# Motor Control
position = 0

def move_forward():
    global position
    position += 10
    print(f"Motor position: {position}")

def move_backward():
    global position
    position -= 10
    print(f"Motor position: {position}")

# Test motor
move_forward()
move_backward()' > motor.py

# Cek status
git status
```

**Output:**
```
On branch main
No commits yet
Untracked files:
  (use "git add <file>..." to include in what will be committed)
	motor.py

nothing added to commit but untracked files present (use "git add" to track)
```

**💡 Penjelasan:** File `motor.py` berada di **Working Directory** tapi belum dilacak Git.

#### Tahap 1: Pindahkan ke Staging Area

```bash
git add motor.py
git status
```

**Output:**
```
On branch main
No commits yet
Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
	new file:   motor.py
```

**💡 Penjelasan:** Sekarang file berada di **Staging Area**, siap untuk di-commit.

#### Tahap 2: Commit ke Repository

```bash
git commit -m "Add basic motor control"
git status
```

**Output:**
```
On branch main
nothing to commit, working tree clean
```

**💡 Penjelasan:** File sudah tersimpan permanent di **Repository**!

### 🔬 Percobaan 3: Memahami Git Log dan History

```bash
git log
```

**Output:**
```
commit a1b2c3d4e5f6g7h8i9j0 (HEAD -> main)
Author: John Doe <john.doe@email.com>
Date:   Mon Jan 15 10:30:00 2024 +0700

    Add basic motor control
```

**Variasi git log yang berguna:**

```bash
# Log singkat (satu baris per commit)
git log --oneline

# Log dengan grafik branch
git log --graph --oneline

# Log hanya 5 commit terakhir
git log -5
```

### 🔬 Percobaan 4: Mengubah File dan Melihat Perbedaan

```bash
# Tambahkan fungsi stop motor
echo '
def stop():
    print("Motor stopped")' >> motor.py

# Lihat perbedaannya
git diff
```

**Output:**
```diff
diff --git a/motor.py b/motor.py
index 1234567..abcdefg 100644
--- a/motor.py
+++ b/motor.py
@@ -10,3 +10,6 @@ def move_backward():
 # Test motor
 move_forward()
 move_backward()
+
+def stop():
+    print("Motor stopped")
```

**💡 Penjelasan:**
- `+` = baris yang ditambah (warna hijau di terminal)
- `-` = baris yang dihapus (warna merah di terminal)

```bash
# Commit perubahan
git add motor.py
git commit -m "Add stop function"
```

### 🔬 Percobaan 5: Membuat dan Bekerja dengan Branch

**Mengapa butuh branch?**
Bayangkan Anda ingin menambah sensor ultrasonic ke robot tapi tidak ingin merusak kode motor yang sudah jalan. Branch memungkinkan Anda bekerja di "jalur paralel".

```bash
# Lihat branch yang ada
git branch

# Buat branch baru untuk fitur sensor
git branch fitur-sensor

# Lihat branch lagi
git branch
```

**Output:**
```
  fitur-sensor
* main
```

**💡 Penjelasan:** Tanda `*` menunjukkan branch aktif.

```bash
# Pindah ke branch fitur-sensor
git checkout fitur-sensor

# Cek posisi sekarang
git branch
```

**Output:**
```
* fitur-sensor
  main
```

#### Bekerja di Branch Baru

```bash
# Buat file sensor sederhana
echo '# Sensor Ultrasonic
def read_distance():
    distance = 25  # contoh pembacaan
    print(f"Distance: {distance} cm")
    return distance

def check_obstacle():
    distance = read_distance()
    if distance < 10:
        print("Obstacle detected!")
        return True
    else:
        print("Path clear")
        return False

# Test sensor
check_obstacle()' > sensor.py

# Commit di branch fitur-sensor
git add sensor.py
git commit -m "Add ultrasonic sensor"

# Lihat log
git log --oneline
```

**Output:**
```
b2c3d4e (HEAD -> fitur-sensor) Add ultrasonic sensor
a1b2c3d (main) Add stop function
9z8y7x6 Add basic motor control
```

### 🔬 Percobaan 6: Memahami Merge

Sekarang mari gabungkan fitur sensor ke branch main:

```bash
# Kembali ke branch main
git checkout main

# Cek isi folder (tidak ada sensor.py)
ls

# Merge branch fitur-sensor
git merge fitur-sensor

# Cek isi folder lagi (sekarang ada sensor.py)
ls
```

**Output merge:**
```
Updating a1b2c3d..b2c3d4e
Fast-forward
 sensor.py | 15 +++++++++++++++
 1 file changed, 15 insertions(+)
 create mode 100644 sensor.py
```

**💡 Penjelasan Fast-forward:** Git hanya memindahkan pointer main ke commit terbaru karena tidak ada konflik.

### 🔬 Percobaan 7: Menangani Merge Conflict

Mari kita buat situasi konflik untuk belajar menanganinya:

```bash
# Buat branch baru dari main
git checkout -b fitur-servo

# Tambahkan servo control ke motor.py
echo '
def set_servo(angle):
    if 0 <= angle <= 180:
        print(f"Servo set to {angle} degrees")
    else:
        print("Invalid angle!")' >> motor.py

git add motor.py
git commit -m "Add servo control"

# Kembali ke main dan buat perubahan berbeda di tempat yang sama
git checkout main
echo '
def calibrate():
    print("Motor calibrated")' >> motor.py

git add motor.py
git commit -m "Add calibration"

# Coba merge (akan terjadi konflik!)
git merge fitur-servo
```

**Output konflik:**
```
Auto-merging motor.py
CONFLICT (content): Merge conflict in motor.py
Automatic merge failed; fix conflicts and then commit the result.
```

**Lihat file yang konflik:**
```bash
tail -10 motor.py
```

**Output:**
```python
# Test motor
move_forward()
move_backward()

def stop():
    print("Motor stopped")
<<<<<<< HEAD

def calibrate():
    print("Motor calibrated")
=======

def set_servo(angle):
    if 0 <= angle <= 180:
        print(f"Servo set to {angle} degrees")
    else:
        print("Invalid angle!")
>>>>>>> fitur-servo
```

**💡 Penjelasan marker konflik:**
- `<<<<<<< HEAD` = versi dari branch aktif (main)
- `=======` = pemisah
- `>>>>>>> fitur-servo` = versi dari branch yang di-merge

**Cara menyelesaikan:**

```bash
# Edit file untuk menyelesaikan konflik
# Kita akan gabungkan kedua fungsi
echo '# Motor Control
position = 0

def move_forward():
    global position
    position += 10
    print(f"Motor position: {position}")

def move_backward():
    global position
    position -= 10
    print(f"Motor position: {position}")

def stop():
    print("Motor stopped")

def calibrate():
    print("Motor calibrated")

def set_servo(angle):
    if 0 <= angle <= 180:
        print(f"Servo set to {angle} degrees")
    else:
        print("Invalid angle!")

# Test motor
move_forward()
move_backward()' > motor.py

# Commit penyelesaian konflik
git add motor.py
git commit -m "Merge servo and calibration features"
```

### 🔬 Percobaan 8: Bekerja dengan Remote Repository (GitHub)

```bash
# Abaikan semua file sementara dan log
*.log
*.tmp
*.bak

# Abaikan folder build dan dist
/build/
/dist/

# Abaikan file konfigurasi pribadi
.env
config.local.json
```

#### Setup GitHub

1. Buat akun di [github.com](https://github.com)
2. Buat repository baru dengan nama `robot-arm-controller`
3. **Jangan** centang "Initialize with README"

#### Hubungkan Repository Lokal ke GitHub

```bash
# Tambahkan remote origin
git remote add origin https://github.com/username/robot-arm-controller.git

# Lihat remote yang terdaftar
git remote -v
```

**Output:**
```
origin  https://github.com/username/robot-arm-controller.git (fetch)
origin  https://github.com/username/robot-arm-controller.git (push)
```

#### Push ke GitHub

```bash
# Push branch main ke GitHub
git push -u origin main
```

**💡 Penjelasan `-u origin main`:**
- `-u` = set upstream (koneksi default)
- `origin` = nama remote
- `main` = nama branch

#### Clone Repository dari GitHub

```bash
# Pindah ke folder lain
cd ..
mkdir backup
cd backup

# Clone repository
git clone https://github.com/username/robot-arm-controller.git

# Masuk ke folder hasil clone
cd robot-arm-controller

# Lihat history
git log --oneline
```

### 🔬 Percobaan 9: .gitignore



## 🚨 Mengatasi Masalah Umum

### Problem 1: Commit Message Salah

```bash
# Ubah commit message terakhir
git commit --amend -m "Pesan commit yang benar"
```

### Problem 2: Salah Add File

```bash
# Un-stage file (keluarkan dari staging area)
git reset HEAD namafile.txt

# Atau untuk semua file
git reset HEAD .
```

### Problem 3: Ingin Kembali ke Commit Sebelumnya

```bash
# Lihat history
git log --oneline

# Kembali ke commit tertentu (hati-hati!)
git reset --hard commit_hash

# Alternatif yang lebih aman (buat commit baru yang membatalkan)
git revert commit_hash
```

### Problem 4: File Terlanjur Di-commit

```bash
# Hapus dari Git tapi tetap di working directory
git rm --cached config.json
git commit -m "Remove config file from tracking"

# Tambahkan ke .gitignore
echo "config.json
*.pyc
__pycache__/
.env
*.log" >> .gitignore
git add .gitignore
git commit -m "Add .gitignore for Python and config files"
```

---


## 🎯 Best Practices untuk Robotics

### 1. **Pesan Commit yang Baik**
```bash
# ❌ Buruk
git commit -m "fix"
git commit -m "update motor"

# ✅ Baik  
git commit -m "Fix motor stop function"
git commit -m "Add sensor reading validation"
git commit -m "Improve robot response time"
```

### 2. **Branch Naming Sederhana**
```bash
git checkout -b add-sensor
git checkout -b fix-motor  
git checkout -b test-led
git checkout -b new-feature
```

### 3. **Commit Frequency**
- Commit setelah setiap fungsi selesai
- Commit sebelum coba hal baru
- Commit setelah testing berhasil
- **Jangan** commit kode yang error

### 4. **File Organization Sederhana**
```
simple-robot/
├── main.py        # Program utama
├── motor.py       # Kontrol motor  
├── sensor.py      # Baca sensor
├── led.py         # LED indicator
├── README.md      # Dokumentasi
└── .gitignore     # File yang diabaikan
```

---

## 🔗 Sumber Belajar Lanjutan

1. **Git Official**: [git-scm.com](https://git-scm.com/doc)
2. **GitHub Guides**: [guides.github.com](https://guides.github.com/)
3. **Interactive Git**: [learngitbranching.js.org](https://learngitbranching.js.org/)

---

## 🎉 Selamat!

Anda sudah menguasai Git untuk proyek robotik! Sekarang Anda bisa:

✅ **Manage kode robot** dengan version control  
✅ **Berkolaborasi** dengan tim robotik  
✅ **Backup dan restore** kode dengan aman  
✅ **Experiment** tanpa takut merusak kode  
✅ **Track perubahan** dan debug lebih mudah  

**Next steps:** Coba buat proyek robot sendiri dan praktikkan semua yang sudah dipelajari!

> 💡 **Tips Terakhir:** Git adalah tool yang dipelajari dengan praktek. Jangan takut salah - hampir semua masalah Git bisa diperbaiki! Start small, practice often! 🚀

---

## 🎯 Rangkuman Perintah Penting

| Kategori | Perintah | Fungsi | Kapan Digunakan |
|----------|----------|---------|-----------------|
| **Setup** | `git init` | Inisialisasi repo | Memulai proyek baru |
| **Setup** | `git clone <url>` | Download repo | Bergabung proyek existing |
| **Status** | `git status` | Cek kondisi repo | Sebelum commit |
| **Status** | `git log` | Lihat history | Cek riwayat perubahan |
| **Tracking** | `git add <file>` | Stage file | Sebelum commit |
| **Tracking** | `git add .` | Stage semua | Commit banyak file |
| **Commit** | `git commit -m "pesan"` | Simpan perubahan | Setelah stage |
| **Branch** | `git branch` | Lihat branch | Cek posisi branch |
| **Branch** | `git checkout -b <nama>` | Buat & pindah branch | Fitur baru |
| **Branch** | `git merge <branch>` | Gabung branch | Setelah fitur selesai |
| **Remote** | `git push` | Kirim ke server | Upload perubahan |
| **Remote** | `git pull` | Ambil dari server | Sinkronisasi |

---

## ✅ Checklist Mahir Git untuk Robotics

- [ ] Bisa init repository dan commit kode robot pertama
- [ ] Paham working directory, staging, dan repository
- [ ] Bisa buat dan merge branch untuk fitur robot berbeda
- [ ] Bisa selesaikan merge conflict pada kode C/Python
- [ ] Bisa push/pull dari GitHub untuk proyek robotik
- [ ] Paham workflow Pull Request untuk review kode
- [ ] Bisa menggunakan .gitignore untuk file robotik
- [ ] Bisa reset/revert commit jika ada bug
- [ ] Bisa kolaborasi dalam tim pengembangan robot
- [ ] Bisa manage dependencies dan build tools (Makefile, requirements.txt)

> 💡 **Tips Sukses:** Git adalah skill yang dipelajari dengan praktek. Jangan takut salah - hampir semua masalah Git bisa diperbaiki!
