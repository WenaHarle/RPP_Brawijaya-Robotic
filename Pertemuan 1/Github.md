# 🐙 Panduan GitHub untuk Programmer Robotik

## 🎯 Tujuan Pembelajaran

Setelah mempelajari materi ini, peserta akan mampu:

* ✅ Memahami perbedaan Git vs GitHub dan mengapa GitHub penting untuk proyek robotik
* ✅ Melakukan kolaborasi tim robotik menggunakan GitHub
* ✅ Menggunakan fitur GitHub untuk manajemen proyek robotik
* ✅ Mengatasi workflow GitHub dari clone hingga pull request
* ✅ Menggunakan GitHub Issues untuk tracking bug dan fitur

---

## 🧠 Git vs GitHub: Apa Bedanya?

| Aspek | Git | GitHub |
|-------|-----|--------|
| **Apa itu** | Tool version control | Platform online untuk Git |
| **Lokasi** | Di komputer Anda | Di cloud (internet) |
| **Fungsi** | Melacak perubahan kode | Menyimpan, berbagi, kolaborasi |
| **Analogi** | Kamera (mengambil foto/commit) | Instagram (tempat berbagi foto) |

**Contoh dalam robotik:**
- **Git**: Menyimpan history pengembangan kode robot di laptop
- **GitHub**: Tempat tim robotik berbagi kode dan berkolaborasi online

---

## 🚀 Setup GitHub

### Langkah 1: Buat Akun GitHub

1. Kunjungi [github.com](https://github.com)
2. Klik **"Sign up"**
3. Isi data: username, email, password
4. Pilih **"Free"** account (sudah cukup untuk belajar)

**💡 Tips Username:**
- Gunakan nama yang profesional (misal: `robotik_john`, `jane_robotics`)
- Hindari angka atau simbol aneh
- Akan terlihat di semua kontribusi Anda

### Langkah 2: Setup SSH (Opsional tapi Direkomendasikan)

SSH membuat Anda tidak perlu input password terus-menerus:

```bash
# Generate SSH key
ssh-keygen -t ed25519 -C "your_email@example.com"

# Copy public key
cat ~/.ssh/id_ed25519.pub
```

Lalu paste ke GitHub:
1. GitHub Settings → SSH and GPG keys
2. New SSH key
3. Paste dan save

---

## 🔬 Percobaan GitHub: Dari Clone hingga Kolaborasi

### 🔬 Percobaan 1: Membuat Repository Pertama

#### Di GitHub.com:

1. Klik tombol hijau **"New"** atau ikon **"+"**
2. Isi detail repository:
   - **Repository name**: `robot-sumo-controller`
   - **Description**: `Controller untuk robot sumo competition`
   - **Public** ✅ (bisa dilihat semua orang)
   - **Add README** ✅
   - **Add .gitignore** → pilih "Python"
   - **License** → pilih "MIT License"

3. Klik **"Create repository"**

**💡 Penjelasan pilihan:**
- **Public vs Private**: Public gratis unlimited, Private terbatas
- **README**: File dokumentasi utama (markdown format)
- **.gitignore**: File untuk mengabaikan file tertentu
- **License**: Aturan penggunaan kode Anda

### 🔬 Percobaan 2: Clone Repository

**Clone = Download repository ke komputer lokal**

```bash
# Clone dengan HTTPS
git clone https://github.com/username/robot-sumo-controller.git

# Atau clone dengan SSH (jika sudah setup)
git clone git@github.com:username/robot-sumo-controller.git

# Masuk ke folder
cd robot-sumo-controller

# Lihat isi
ls -la
```

**Output yang diharapkan:**
```
.git/
.gitignore
LICENSE
README.md
```

**💡 Penjelasan:**
- `.git/` = folder Git (jangan dihapus!)
- `.gitignore` = daftar file yang diabaikan Git
- `README.md` = dokumentasi proyek
- `LICENSE` = lisensi penggunaan

### 🔬 Percobaan 3: Workflow Dasar GitHub

Mari buat kode robot sumo sederhana:

```bash
# Buat file main controller
echo '# Robot Sumo Controller
# Main program untuk robot sumo competition

import time

class SumoRobot:
    def __init__(self):
        self.speed = 50
        self.direction = "forward"
        
    def move_forward(self):
        print(f"Robot bergerak maju dengan speed {self.speed}")
        self.direction = "forward"
        
    def turn_left(self):
        print("Robot belok kiri")
        time.sleep(0.5)
        
    def turn_right(self):
        print("Robot belok kanan")
        time.sleep(0.5)
        
    def search_enemy(self):
        print("Mencari lawan...")
        # TODO: Implementasi sensor ultrasonic
        return False
        
    def attack(self):
        print("Menyerang lawan!")
        self.speed = 100
        self.move_forward()

# Main program
if __name__ == "__main__":
    robot = SumoRobot()
    
    while True:
        if robot.search_enemy():
            robot.attack()
        else:
            robot.turn_left()
        
        time.sleep(0.1)
' > sumo_robot.py

# Buat file konfigurasi
echo '{
  "robot_config": {
    "max_speed": 100,
    "turn_speed": 30,
    "sensor_range": 20,
    "competition_mode": true
  },
  "pins": {
    "motor_left": 5,
    "motor_right": 6,
    "sensor_front": 7,
    "led": 13
  }
}' > config.json

# Tambahkan ke staging
git add .

# Commit
git commit -m "Add basic sumo robot controller and config"

# Push ke GitHub
git push origin main
```

**Cek di GitHub.com** - file sudah muncul!

### 🔬 Percobaan 4: Memahami Remote

```bash
# Lihat remote yang terdaftar
git remote -v
```

**Output:**
```
origin  https://github.com/username/robot-sumo-controller.git (fetch)
origin  https://github.com/username/robot-sumo-controller.git (push)
```

**💡 Penjelasan:**
- `origin` = nama default untuk remote utama
- `fetch` = untuk mengambil data dari GitHub
- `push` = untuk mengirim data ke GitHub

```bash
# Tambah remote lain (misal untuk backup)
git remote add backup https://github.com/username/robot-sumo-backup.git

# Lihat semua remote
git remote -v

# Hapus remote
git remote remove backup
```

### 🔬 Percobaan 5: Kolaborasi Tim - Fork dan Pull Request

**Skenario**: Teman Anda ingin membantu mengembangkan robot sumo.

#### Sebagai Kontributor (Teman Anda):

1. **Fork repository** di GitHub.com:
   - Kunjungi `https://github.com/username/robot-sumo-controller`
   - Klik tombol **"Fork"** (pojok kanan atas)
   - Pilih akun tujuan fork

2. **Clone fork** ke komputer:
```bash
git clone https://github.com/teman/robot-sumo-controller.git
cd robot-sumo-controller
```

3. **Buat branch untuk fitur baru**:
```bash
git checkout -b add-sensor-ultrasonic
```

4. **Tambah fitur sensor**:
```bash
echo '# Sensor Ultrasonic untuk Robot Sumo
import time

class UltrasonicSensor:
    def __init__(self, trigger_pin, echo_pin):
        self.trigger_pin = trigger_pin
        self.echo_pin = echo_pin
        
    def measure_distance(self):
        # Simulasi pembacaan sensor
        # Di implementasi nyata, gunakan GPIO
        import random
        distance = random.randint(5, 50)
        print(f"Distance measured: {distance} cm")
        return distance
        
    def is_enemy_detected(self, max_range=20):
        distance = self.measure_distance()
        return distance <= max_range

# Test sensor
if __name__ == "__main__":
    sensor = UltrasonicSensor(trigger_pin=7, echo_pin=8)
    
    for i in range(5):
        if sensor.is_enemy_detected():
            print("Enemy detected! Attack!")
        else:
            print("No enemy, continue searching...")
        time.sleep(1)
' > ultrasonic_sensor.py

git add ultrasonic_sensor.py
git commit -m "Add ultrasonic sensor class for enemy detection"
```

5. **Push branch ke fork**:
```bash
git push origin add-sensor-ultrasonic
```

6. **Buat Pull Request** di GitHub.com:
   - Kunjungi fork Anda di GitHub
   - Klik **"Compare & pull request"**
   - Isi title: `Add ultrasonic sensor for enemy detection`
   - Isi description:
   ```
   ## What this PR does
   - Adds UltrasonicSensor class
   - Implements distance measurement
   - Adds enemy detection logic
   
   ## Testing
   - Tested with simulated sensor readings
   - All functions working as expected
   
   ## Next steps
   - Integration with main robot class
   - Real GPIO implementation needed
   ```
   - Klik **"Create pull request"**

#### Sebagai Pemilik Repository (Anda):

1. **Review Pull Request**:
   - Buka tab **"Pull requests"** di repository
   - Klik PR dari teman
   - Lihat **"Files changed"** untuk melihat kode

2. **Beri komentar** (jika ada saran):
   - Klik baris kode yang ingin dikomentari
   - Tulis feedback: "Good work! Maybe add error handling?"

3. **Merge Pull Request**:
   - Klik **"Merge pull request"**
   - Pilih **"Create a merge commit"**
   - Klik **"Confirm merge"**

4. **Update repository lokal**:
```bash
git checkout main
git pull origin main
```

Sekarang kode sensor sudah ada di repository Anda!

### 🔬 Percobaan 6: Menangani Konflik dalam Kolaborasi

**Skenario**: Dua orang mengubah file yang sama secara bersamaan.

#### Simulasi Konflik:

1. **Anda mengubah** `sumo_robot.py`:
```bash
# Edit file, tambah method baru di akhir class
echo '    
    def emergency_stop(self):
        print("EMERGENCY STOP!")
        self.speed = 0' >> sumo_robot.py

git add sumo_robot.py
git commit -m "Add emergency stop feature"
git push origin main
```

2. **Teman juga mengubah** `sumo_robot.py` di tempat berbeda tapi push lebih dulu:
```bash
# Simulasi: teman menambah method lain
git checkout -b add-status
echo '    
    def get_status(self):
        return f"Speed: {self.speed}, Direction: {self.direction}"' >> sumo_robot.py

git add sumo_robot.py
git commit -m "Add status reporting"
git push origin add-status
```

3. **Teman membuat PR**, Anda merge

4. **Sekarang Anda pull** untuk update:
```bash
git pull origin main
```

**Jika ada konflik**, Git akan memberi tahu dan Anda perlu resolve manually seperti yang dipelajari di panduan Git sebelumnya.

### 🔬 Percobaan 7: GitHub Issues - Tracking Bug dan Fitur

**GitHub Issues** = sistem untuk melacak bug, feature request, dan diskusi.

#### Membuat Issue:

1. Di repository GitHub, klik tab **"Issues"**
2. Klik **"New issue"**
3. Isi detail:
   - **Title**: `Robot tidak bisa deteksi lawan dari jarak dekat`
   - **Description**:
   ```
   ## Bug Description
   Robot sumo tidak bisa mendeteksi lawan ketika jarak < 5cm
   
   ## Steps to Reproduce
   1. Jalankan program utama
   2. Letakkan objek di depan robot (jarak 3cm)
   3. Robot tetap mencari lawan
   
   ## Expected Behavior
   Robot harus langsung menyerang
   
   ## Actual Behavior
   Robot terus berputar mencari lawan
   
   ## Environment
   - Python 3.9
   - Raspberry Pi 4
   - Ultrasonic sensor HC-SR04
   
   ## Priority
   High - ini critical untuk competition
   ```

4. **Assign** ke developer
5. **Add labels**: `bug`, `high-priority`, `competition`
6. Klik **"Submit new issue"**

#### Menangani Issue:

```bash
# Buat branch untuk fix bug
git checkout -b fix-close-range-detection

# Edit kode untuk fix bug
# ... lakukan perubahan ...

git add .
git commit -m "Fix close range detection issue

- Update sensor threshold from 20cm to 3cm  
- Add minimum detection range validation
- Fixes #1"

git push origin fix-close-range-detection
```

**💡 Tips commit message**:
- `Fixes #1` otomatis close issue #1 ketika di-merge
- Bisa juga: `Closes #1`, `Resolves #1`

### 🔬 Percobaan 8: GitHub Projects untuk Manajemen Tim

**GitHub Projects** = Kanban board untuk organize pekerjaan tim.

#### Setup Project:

1. Di repository, tab **"Projects"**
2. **"New project"** → pilih **"Board"**
3. Nama: `Robot Sumo Development`

#### Buat Kolom:
- **Backlog** (fitur yang akan dikerjakan)
- **In Progress** (sedang dikerjakan)  
- **Review** (menunggu review)
- **Done** (selesai)

#### Tambah Cards:
- **"Implement PID control"** → Backlog
- **"Add LED status indicators"** → Backlog
- **"Calibrate motor speeds"** → In Progress
- **"Fix sensor bug"** → Review

---

## 🌟 GitHub Features untuk Robotics

### 1. **Releases & Versioning**

Untuk marking versi stabil robot:

```bash
# Tag version
git tag -a v1.0 -m "Robot Sumo v1.0 - Competition Ready"
git push origin v1.0
```

Di GitHub → **Releases** → **Create new release**

### 2. **Wiki untuk Dokumentasi**

Tempat dokumentasi detail:
- Setup hardware
- Wiring diagram  
- Troubleshooting guide
- Competition strategies

### 3. **Actions untuk CI/CD**

Automated testing setiap ada push:

`.github/workflows/test.yml`:
```yaml
name: Test Robot Code

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Setup Python
      uses: actions/setup-python@v2
      with:
        python-version: 3.9
    - name: Run Tests
      run: python -m pytest tests/
```

### 4. **Security & Code Scanning**

GitHub otomatis scan kode untuk:
- Vulnerabilities di dependencies
- Security issues
- Code quality problems

---

## 🚀 Workflow GitHub untuk Tim Robotik

### Daily Workflow:

```bash
# 1. Morning: Update local repo
git checkout main
git pull origin main

# 2. Buat branch untuk task hari ini
git checkout -b fix-motor-calibration

# 3. Coding...
# Edit files, test, debug

# 4. Commit progress
git add .
git commit -m "WIP: motor calibration improvements"

# 5. Push untuk backup
git push origin fix-motor-calibration

# 6. Evening: jika selesai, buat PR
# Lakukan di GitHub.com
```

### Competition Workflow:

```bash
# 1. Week sebelum competition
git checkout -b competition-prep
# Optimize code, fix bugs

# 2. Hari H-1: freeze code
git tag -a competition-v1.0 -m "Code for competition"
git push origin competition-v1.0

# 3. Competition day: hanya bug fixes critical
git checkout -b hotfix-competition
# Fix hanya yang benar-benar perlu
```

---

## 🎯 Best Practices GitHub untuk Robotics

### 1. **Repository Structure yang Baik**

```
robot-sumo/
├── src/                    # Source code
│   ├── main.py
│   ├── sensors/
│   ├── motors/
│   └── controllers/
├── config/                 # Configuration files
├── tests/                  # Unit tests
├── docs/                   # Documentation
│   ├── wiring.md
│   ├── setup.md
│   └── troubleshooting.md
├── hardware/               # Hardware files
│   ├── 3d_models/
│   └── schematics/
├── .gitignore
├── README.md
├── requirements.txt        # Python dependencies
└── LICENSE
```

### 2. **README yang Informatif**

```markdown
# Robot Sumo Controller 🤖

Competition-ready sumo robot controller with sensor integration.

## Features
- ✅ Ultrasonic enemy detection
- ✅ Motor speed control
- ✅ Emergency stop
- ✅ Competition mode

## Quick Start
```bash
pip install -r requirements.txt
python src/main.py
```

## Hardware Required
- Raspberry Pi 4
- 2x DC Motors
- HC-SR04 Ultrasonic sensor
- L298N Motor driver

## Competition Results
- 🏆 1st Place - Regional Robotics 2024
- 🥈 2nd Place - National Championship 2024
```

### 3. **Issue Templates**

`.github/ISSUE_TEMPLATE/bug_report.md`:
```markdown
---
name: Bug Report
about: Create a report to help us improve the robot
title: '[BUG] '
labels: bug
---

## Bug Description
A clear description of the bug.

## Robot Environment
- Hardware: [e.g. Raspberry Pi 4]
- Sensors: [e.g. HC-SR04]
- Python version: [e.g. 3.9]

## Steps to Reproduce
1. Go to '...'
2. Click on '....'
3. See error

## Expected vs Actual Behavior
**Expected:** Robot should...
**Actual:** Robot does...

## Additional Context
Add any other context about the problem here.
```

### 4. **Pull Request Template**

`.github/pull_request_template.md`:
```markdown
## What does this PR do?
Brief description of changes.

## Type of Change
- [ ] Bug fix
- [ ] New feature  
- [ ] Hardware change
- [ ] Documentation update

## Testing
- [ ] Tested on real hardware
- [ ] Unit tests pass
- [ ] No breaking changes

## Hardware Impact
- [ ] No hardware changes needed
- [ ] New wiring required
- [ ] New components needed

## Competition Ready?
- [ ] Yes, safe for competition
- [ ] No, needs more testing
```

---

## 🚨 Troubleshooting GitHub

### Problem 1: Can't Push to GitHub

```bash
# Error: remote rejected
git pull origin main
# Resolve conflicts if any
git push origin main
```

### Problem 2: Accidentally Committed Large Files

```bash
# Remove from Git but keep in working directory
git rm --cached large_video.mp4
echo "*.mp4" >> .gitignore
git add .gitignore
git commit -m "Remove large files and update gitignore"
```

### Problem 3: Wrong Repository Cloned

```bash
# Change remote URL
git remote set-url origin https://github.com/correct-user/correct-repo.git
git remote -v  # verify
```

### Problem 4: Need to Sync Fork with Original

```bash
# Add upstream remote
git remote add upstream https://github.com/original-user/original-repo.git

# Sync with upstream
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

---

## 🔧 GitHub CLI (Bonus)

Install GitHub CLI untuk workflow lebih cepat:

```bash
# Install (Ubuntu/Debian)
sudo apt install gh

# Login
gh auth login

# Create repo from command line
gh repo create robot-line-follower --public

# Create issue
gh issue create --title "Add line sensor" --body "Need to implement line following"

# Create PR
gh pr create --title "Add PID controller" --body "Implements PID for smooth movement"

# View PRs
gh pr list

# Merge PR
gh pr merge 123
```

---

## 🎯 Rangkuman Commands GitHub

| Kategori | Command | Fungsi | Kapan Digunakan |
|----------|---------|---------|-----------------|
| **Clone** | `git clone <url>` | Download repo | Join project baru |
| **Remote** | `git remote -v` | Lihat remote | Cek koneksi GitHub |
| **Remote** | `git remote add origin <url>` | Tambah remote | Setup repo baru |
| **Sync** | `git pull origin main` | Ambil update | Setiap mulai kerja |
| **Sync** | `git push origin main` | Kirim update | Setelah commit |
| **Branch** | `git push origin <branch>` | Push branch | Buat PR |
| **Fork** | `gh repo fork` | Fork repo | Kontribusi ke project lain |
| **Issues** | `gh issue create` | Buat issue | Report bug/request |

---

## 🎉 Selamat!

Anda sudah menguasai GitHub untuk kolaborasi robotik! Sekarang Anda bisa:

✅ **Berkolaborasi** dengan tim robotik secara efektif  
✅ **Mengelola proyek** dengan Issues dan Projects  
✅ **Code review** untuk quality assurance  
✅ **Backup dan sync** kode antar developer  
✅ **Track progress** dan planning development  
✅ **Showcase** proyek robotik ke komunitas  

**Next steps:** 
1. Buat repository untuk proyek robot Anda
2. Ajak teman untuk kolaborasi
3. Gunakan Issues untuk planning
4. Praktek workflow Pull Request

> 💡 **Tips Terakhir:** GitHub adalah tempat developer berkumpul. Aktif di GitHub = networking dengan robotics community worldwide! 🌍
