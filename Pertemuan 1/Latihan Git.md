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
# atau gunakan perintah modern:
git switch fitur-sensor

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
git switch main

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
git switch main
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

### 🔬 Percobaan 9: Workflow Kolaborasi

Simulasi bekerja dalam tim:

```bash
# Buat branch untuk fitur baru
git checkout -b fitur-led

# Tambahkan kontrol LED sederhana
echo '# LED Control
led_status = False

def turn_on():
    global led_status
    led_status = True
    print("LED ON")

def turn_off():
    global led_status
    led_status = False
    print("LED OFF")

def blink():
    turn_on()
    turn_off()
    print("LED blinked")

# Test LED
blink()' > led.py

git add led.py
git commit -m "Add LED control for robot status"

# Push branch ke GitHub
git push -u origin fitur-led
```

**Di GitHub:**
1. Buat Pull Request dari `fitur-led` ke `main`
2. Review kode
3. Merge Pull Request

**Kembali ke lokal:**
```bash
# Update main branch
git switch main
git pull origin main

# Hapus branch yang sudah di-merge
git branch -d fitur-led
```

---

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

## 📋 Latihan Praktis Mandiri

### Latihan 1: Sistem Kontrol Robot

1. **Setup:**
   ```bash
   mkdir simple-robot
   cd simple-robot
   git init
   ```

2. **Buat struktur proyek sederhana:**
   - `main.py` (program utama)
   - `motor.py` (kontrol motor)
   - `sensor.py` (baca sensor)
   - `led.py` (indikator LED)
   - `README.md` (dokumentasi)

3. **Contoh file main.py:**
   ```bash
   echo '# Robot Main Program
import motor
import sensor
import led

def robot_start():
    print("Robot starting...")
    led.turn_on()
    
    # Cek sensor
    if sensor.check_obstacle():
        motor.stop()
        led.blink()
    else:
        motor.move_forward()
    
    print("Robot ready!")

# Run robot
robot_start()' > main.py
   ```

4. **Workflow latihan:**
   - Buat branch untuk setiap modul
   - Commit setiap perubahan
   - Merge ke main setelah testing

### Latihan 2: Sensor dan Aktuator

1. **Buat proyek sensor:**
   ```bash
   mkdir robot-sensors
   cd robot-sensors
   git init
   ```

2. **File sensor sederhana:**
   ```bash
   # Temperature sensor
   echo '# Temperature Sensor
def read_temp():
    temp = 25.5  # contoh suhu
    print(f"Temperature: {temp}°C")
    return temp

def check_overheat():
    temp = read_temp()
    if temp > 50:
        print("OVERHEAT WARNING!")
        return True
    return False' > temp_sensor.py
   
   # Light sensor  
   echo '# Light Sensor
def read_light():
    light = 512  # contoh nilai ADC 0-1024
    print(f"Light level: {light}")
    return light

def is_dark():
    light = read_light()
    return light < 200' > light_sensor.py
   ```

3. **Integration file:**
   ```bash
   echo '# Sensor Integration
import temp_sensor
import light_sensor

def check_all_sensors():
    print("=== Sensor Status ===")
    
    # Check temperature
    if temp_sensor.check_overheat():
        print("⚠️  Temperature warning")
    else:
        print("✅ Temperature OK")
    
    # Check light
    if light_sensor.is_dark():
        print("🌙 Dark environment")
    else:
        print("☀️  Bright environment")

# Run check
check_all_sensors()' > sensor_check.py
   ```

### Latihan 3: Proyek Tim Line Follower

1. **Skenario tim 4 orang:**
   - **Person A**: Motor control
   - **Person B**: Line sensor
   - **Person C**: Decision logic
   - **Person D**: LED indicators

2. **Pembagian file:**
   ```bash
   # Person A - Motor
   echo '# Motor Control for Line Follower
speed = 100

def go_straight():
    print(f"Motors: L={speed}, R={speed}")

def turn_left():
    print(f"Motors: L={speed//2}, R={speed}")

def turn_right():
    print(f"Motors: L={speed}, R={speed//2}")

def stop():
    print("Motors: STOP")' > motor_control.py
   
   # Person B - Sensor
   echo '# Line Sensor Array
sensors = [0, 0, 1, 0, 0]  # 5 sensor array

def read_sensors():
    # Simulasi pembacaan sensor
    print(f"Sensors: {sensors}")
    return sensors

def find_line_position():
    sensors = read_sensors()
    # Hitung posisi garis (-2 to +2)
    position = 0
    total = sum(sensors)
    if total > 0:
        weighted_sum = sum(i * sensors[i] for i in range(5))
        position = (weighted_sum / total) - 2
    return position' > line_sensor.py
   
   # Person C - Logic
   echo '# Line Following Logic
import motor_control
import line_sensor

def follow_line():
    position = line_sensor.find_line_position()
    
    if abs(position) < 0.5:
        motor_control.go_straight()
    elif position > 0.5:
        motor_control.turn_right()
    elif position < -0.5:
        motor_control.turn_left()
    else:
        motor_control.stop()
        
    print(f"Line position: {position}")

# Main loop
for i in range(5):
    follow_line()' > line_following.py
   ```

### Latihan 4: Robot Penjaga Sederhana

```bash
mkdir guard-robot
cd guard-robot
git init

# Main program
echo '# Guard Robot
import time

# Sensor simulasi
def detect_motion():
    import random
    return random.choice([True, False])

# Aksi robot
def sound_alarm():
    print("🚨 ALARM! Motion detected!")

def patrol():
    print("🤖 Patrolling...")

def sleep_mode():
    print("😴 Sleep mode")

# Main loop
def guard_system():
    print("Guard Robot Active")
    
    for minute in range(10):  # Simulasi 10 menit
        print(f"Time: {minute}:00")
        
        if detect_motion():
            sound_alarm()
            patrol()
        else:
            sleep_mode()
        
        time.sleep(1)  # Tunggu 1 detik
    
    print("Guard duty complete")

# Start guard
guard_system()' > guard_robot.py
``` ## 📝 Template .gitignore untuk Proyek Robotik

```bash
# Python
__pycache__/
*.pyc
*.pyo
*.pyd
.Python
*.so

# Virtual environment
venv/
env/
.env

# IDE files
.vscode/
*.swp
*~

# Log files
*.log
debug.txt

# Configuration files
config.json
settings.ini

# Data files
data/
*.csv
*.txt

# OS files
.DS_Store
Thumbs.db

# Backup files
*.bak
*.backup
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

## ✅ Checklist Mahir Git untuk Robotics

- [ ] Bisa init repository dan commit kode robot pertama
- [ ] Paham working directory, staging, dan repository  
- [ ] Bisa buat dan merge branch untuk fitur robot berbeda
- [ ] Bisa selesaikan merge conflict pada kode Python
- [ ] Bisa push/pull dari GitHub untuk proyek robotik
- [ ] Paham workflow Pull Request untuk review kode
- [ ] Bisa menggunakan .gitignore untuk file Python
- [ ] Bisa reset/revert commit jika ada bug
- [ ] Bisa kolaborasi dalam tim pengembangan robot
- [ ] Bisa manage proyek robotik dengan Git

---

## 🎮 Challenge Akhir: Robot Simulator

Buat proyek robot simulator sederhana dengan Git:

```bash
mkdir robot-simulator
cd robot-simulator
git init

# File utama
echo '# Robot Simulator
import random
import time

# Robot state
robot = {
    "x": 0,
    "y": 0, 
    "battery": 100,
    "status": "idle"
}

def move_robot(direction):
    """Move robot in direction"""
    global robot
    
    if robot["battery"] <= 0:
        print("❌ Battery empty!")
        return
        
    if direction == "up":
        robot["y"] += 1
    elif direction == "down":
        robot["y"] -= 1
    elif direction == "left":
        robot["x"] -= 1
    elif direction == "right":
        robot["x"] += 1
    
    robot["battery"] -= 10
    print(f"🤖 Robot at ({robot["x"]}, {robot["y"]}) - Battery: {robot["battery"]}%")

def scan_area():
    """Scan for obstacles"""
    obstacles = random.choice([True, False])
    if obstacles:
        print("⚠️  Obstacle detected!")
    else:
        print("✅ Area clear")
    return obstacles

def charge_battery():
    """Charge robot battery"""
    global robot
    robot["battery"] = 100
    print("🔋 Battery charged!")

def robot_demo():
    """Demo robot movement"""
    print("🚀 Robot Simulator Started")
    
    # Move robot around
    moves = ["up", "right", "down", "left"]
    
    for move in moves:
        move_robot(move)
        scan_area()
        time.sleep(1)
        
        # Charge if battery low
        if robot["battery"] <= 20:
            charge_battery()
    
    print("🏁 Demo complete!")

# Run demo
if __name__ == "__main__":
    robot_demo()' > robot_sim.py

# Commit
git add robot_sim.py
git commit -m "Add basic robot simulator"

# Test
python robot_sim.py
```

**Pengembangan lanjutan:**
1. Buat branch untuk fitur baru (obstacle avoidance, pathfinding, dll)
2. Tambahkan lebih banyak sensor simulasi
3. Buat GUI sederhana jika mau
4. Upload ke GitHub dan minta feedback

---

## 🔗 Sumber Belajar Lanjutan

1. **Git Official**: [git-scm.com](https://git-scm.com/doc)
2. **GitHub Guides**: [guides.github.com](https://guides.github.com/)
3. **Interactive Git**: [learngitbranching.js.org](https://learngitbranching.js.org/)
4. **Python untuk Robotik**: [realpython.com](https://realpython.com/)

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

## 🎯 Best Practices untuk Robotics

### 1. **Pesan Commit yang Baik**
```bash
# ❌ Buruk
git commit -m "fix bug"
git commit -m "update"

# ✅ Baik  
git commit -m "Fix PID controller oscillation in motor control"
git commit -m "Add emergency stop function to robot arm"
git commit -m "Improve sensor fusion algorithm accuracy"
```

### 2. **Branch Naming Convention**
```bash
# Format: jenis/deskripsi-singkat
git checkout -b feature/ultrasonic-sensor
git checkout -b bugfix/pid-oscillation  
git checkout -b hotfix/emergency-stop
git checkout -b experiment/new-algorithm
```

### 3. **Commit Frequency**
- Commit setelah setiap fungsi selesai
- Commit sebelum eksperimen besar
- Commit setelah testing berhasil
- **Jangan** commit kode yang tidak bisa di-compile

### 4. **File Organization**
```
robot-project/
├── src/           # Source code
├── include/       # Header files (C/C++)
├── tests/         # Unit tests
├── docs/          # Documentation
├── config/        # Configuration files  
├── scripts/       # Build/deployment scripts
├── data/          # Sample data, calibration
└── hardware/      # PCB designs, CAD files
```

---

## 🔗 Sumber Belajar Lanjutan

1. **Official Git Documentation**: [git-scm.com](https://git-scm.com/doc)
2. **Interactive Tutorial**: [learngitbranching.js.org](https://learngitbranching.js.org/)
3. **GitHub Guides**: [guides.github.com](https://guides.github.com/)
4. **Git Cheat Sheet**: [education.github.com/git-cheat-sheet-education.pdf](https://education.github.com/git-cheat-sheet-education.pdf)

---

> 💡 **Tips Sukses:** Git adalah skill yang dipelajari dengan praktek. Jangan takut salah - hampir semua masalah Git bisa diperbaiki!
