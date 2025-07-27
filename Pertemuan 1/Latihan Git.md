# 📚 Panduan Git

## 🎯 Tujuan Pembelajaran

Setelah mempelajari materi ini, peserta akan mampu:

* ✅ Memahami konsep version control dan mengapa penting
* ✅ Menggunakan Git untuk mengelola proyek dengan percaya diri
* ✅ Memahami workflow Git dari dasar hingga kolaborasi
* ✅ Mengatasi masalah umum yang sering terjadi dalam Git
* ✅ Bekerja dengan GitHub untuk kolaborasi tim

---

## 🧠 Memahami Konsep Dasar Git

### Apa itu Version Control?

Bayangkan Anda sedang menulis skripsi. Setiap kali mengubah isi, Anda menyimpan dengan nama berbeda:
- `skripsi_v1.docx`
- `skripsi_v2.docx`
- `skripsi_final.docx`
- `skripsi_final_revisi.docx`
- `skripsi_final_revisi_beneran.docx`

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
# Buat folder proyek
mkdir proyek-website
cd proyek-website

# Inisialisasi Git
git init
```

**Output:**
```
Initialized empty Git repository in /path/proyek-website/.git/
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
# Buat file HTML sederhana
echo '<!DOCTYPE html>
<html>
<head>
    <title>Website Saya</title>
</head>
<body>
    <h1>Selamat datang!</h1>
</body>
</html>' > index.html

# Cek status
git status
```

**Output:**
```
On branch main
No commits yet
Untracked files:
  (use "git add <file>..." to include in what will be committed)
	index.html

nothing added to commit but untracked files present (use "git add" to track)
```

**💡 Penjelasan:** File `index.html` berada di **Working Directory** tapi belum dilacak Git.

#### Tahap 1: Pindahkan ke Staging Area

```bash
git add index.html
git status
```

**Output:**
```
On branch main
No commits yet
Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
	new file:   index.html
```

**💡 Penjelasan:** Sekarang file berada di **Staging Area**, siap untuk di-commit.

#### Tahap 2: Commit ke Repository

```bash
git commit -m "Tambah halaman utama website"
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

    Tambah halaman utama website
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
# Ubah file index.html
echo '    <p>Ini adalah paragraph baru!</p>' >> index.html

# Lihat perbedaannya
git diff
```

**Output:**
```diff
diff --git a/index.html b/index.html
index 1234567..abcdefg 100644
--- a/index.html
+++ b/index.html
@@ -5,4 +5,5 @@
 </head>
 <body>
     <h1>Selamat datang!</h1>
+    <p>Ini adalah paragraph baru!</p>
 </body>
```

**💡 Penjelasan:**
- `+` = baris yang ditambah (warna hijau di terminal)
- `-` = baris yang dihapus (warna merah di terminal)

```bash
# Commit perubahan
git add index.html
git commit -m "Tambah paragraph baru di halaman utama"
```

### 🔬 Percobaan 5: Membuat dan Bekerja dengan Branch

**Mengapa butuh branch?**
Bayangkan Anda ingin menambah fitur baru tapi tidak ingin merusak kode yang sudah jalan. Branch memungkinkan Anda bekerja di "jalur paralel".

```bash
# Lihat branch yang ada
git branch

# Buat branch baru untuk fitur contact
git branch fitur-contact

# Lihat branch lagi
git branch
```

**Output:**
```
  fitur-contact
* main
```

**💡 Penjelasan:** Tanda `*` menunjukkan branch aktif.

```bash
# Pindah ke branch fitur-contact
git checkout fitur-contact
# atau gunakan perintah modern:
git switch fitur-contact

# Cek posisi sekarang
git branch
```

**Output:**
```
* fitur-contact
  main
```

#### Bekerja di Branch Baru

```bash
# Buat halaman contact
echo '<!DOCTYPE html>
<html>
<head>
    <title>Kontak - Website Saya</title>
</head>
<body>
    <h1>Hubungi Kami</h1>
    <p>Email: kontak@websitesaya.com</p>
    <p>Telepon: 021-1234567</p>
</body>
</html>' > contact.html

# Commit di branch fitur-contact
git add contact.html
git commit -m "Tambah halaman contact"

# Lihat log
git log --oneline
```

**Output:**
```
b2c3d4e (HEAD -> fitur-contact) Tambah halaman contact
a1b2c3d (main) Tambah paragraph baru di halaman utama
9z8y7x6 Tambah halaman utama website
```

### 🔬 Percobaan 6: Memahami Merge

Sekarang mari gabungkan fitur contact ke branch main:

```bash
# Kembali ke branch main
git switch main

# Cek isi folder (tidak ada contact.html)
ls

# Merge branch fitur-contact
git merge fitur-contact

# Cek isi folder lagi (sekarang ada contact.html)
ls
```

**Output merge:**
```
Updating a1b2c3d..b2c3d4e
Fast-forward
 contact.html | 10 ++++++++++
 1 file changed, 10 insertions(+)
 create mode 100644 contact.html
```

**💡 Penjelasan Fast-forward:** Git hanya memindahkan pointer main ke commit terbaru karena tidak ada konflik.

### 🔬 Percobaan 7: Menangani Merge Conflict

Mari kita buat situasi konflik untuk belajar menanganinya:

```bash
# Buat branch baru dari main
git checkout -b fitur-header

# Ubah index.html
sed -i 's/<h1>Selamat datang!<\/h1>/<h1>Selamat Datang di Website Keren!<\/h1>/' index.html

git add index.html
git commit -m "Update header menjadi lebih menarik"

# Kembali ke main dan buat perubahan berbeda di tempat yang sama
git switch main
sed -i 's/<h1>Selamat datang!<\/h1>/<h1>Welcome to My Site!<\/h1>/' index.html

git add index.html
git commit -m "Update header ke bahasa Inggris"

# Coba merge (akan terjadi konflik!)
git merge fitur-header
```

**Output konflik:**
```
Auto-merging index.html
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```

**Lihat file yang konflik:**
```bash
cat index.html
```

**Output:**
```html
<!DOCTYPE html>
<html>
<head>
    <title>Website Saya</title>
</head>
<body>
<<<<<<< HEAD
    <h1>Welcome to My Site!</h1>
=======
    <h1>Selamat Datang di Website Keren!</h1>
>>>>>>> fitur-header
    <p>Ini adalah paragraph baru!</p>
</body>
</html>
```

**💡 Penjelasan marker konflik:**
- `<<<<<<< HEAD` = versi dari branch aktif (main)
- `=======` = pemisah
- `>>>>>>> fitur-header` = versi dari branch yang di-merge

**Cara menyelesaikan:**

```bash
# Edit file untuk menyelesaikan konflik
# Pilih salah satu atau gabungkan keduanya
echo '<!DOCTYPE html>
<html>
<head>
    <title>Website Saya</title>
</head>
<body>
    <h1>Selamat Datang di Website Keren!</h1>
    <p>Ini adalah paragraph baru!</p>
</body>
</html>' > index.html

# Commit penyelesaian konflik
git add index.html
git commit -m "Resolve merge conflict: gunakan header bahasa Indonesia"
```

### 🔬 Percobaan 8: Bekerja dengan Remote Repository (GitHub)

#### Setup GitHub

1. Buat akun di [github.com](https://github.com)
2. Buat repository baru dengan nama `proyek-website`
3. **Jangan** centang "Initialize with README"

#### Hubungkan Repository Lokal ke GitHub

```bash
# Tambahkan remote origin
git remote add origin https://github.com/username/proyek-website.git

# Lihat remote yang terdaftar
git remote -v
```

**Output:**
```
origin  https://github.com/username/proyek-website.git (fetch)
origin  https://github.com/username/proyek-website.git (push)
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
git clone https://github.com/username/proyek-website.git

# Masuk ke folder hasil clone
cd proyek-website

# Lihat history
git log --oneline
```

### 🔬 Percobaan 9: Workflow Kolaborasi

Simulasi bekerja dalam tim:

```bash
# Buat branch untuk fitur baru
git checkout -b fitur-footer

# Tambahkan footer
echo '    <footer>
        <p>&copy; 2024 Website Saya. All rights reserved.</p>
    </footer>' >> index.html

git add index.html
git commit -m "Tambah footer copyright"

# Push branch ke GitHub
git push -u origin fitur-footer
```

**Di GitHub:**
1. Buat Pull Request dari `fitur-footer` ke `main`
2. Review kode
3. Merge Pull Request

**Kembali ke lokal:**
```bash
# Update main branch
git switch main
git pull origin main

# Hapus branch yang sudah di-merge
git branch -d fitur-footer
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
git rm --cached file_rahasia.txt
git commit -m "Remove sensitive file from tracking"

# Tambahkan ke .gitignore
echo "file_rahasia.txt" >> .gitignore
git add .gitignore
git commit -m "Add .gitignore"
```

---

## 📋 Latihan Praktis Mandiri

### Latihan 1: Blog Pribadi

1. **Setup:**
   ```bash
   mkdir blog-pribadi
   cd blog-pribadi
   git init
   ```

2. **Buat struktur:**
   - `index.html` (halaman utama)
   - `about.html` (tentang saya)
   - `css/style.css` (stylesheet)
   - `posts/` (folder untuk posting blog)

3. **Workflow:**
   - Buat branch untuk setiap fitur baru
   - Commit perubahan secara berkala
   - Merge ke main setelah fitur selesai

4. **Challenge:**
   - Buat konflik sengaja dan selesaikan
   - Upload ke GitHub
   - Buat Pull Request

### Latihan 2: Proyek Tim (Simulasi)

1. **Clone repository teman** (atau buat dummy)
2. **Buat branch untuk kontribusi:**
   ```bash
   git checkout -b kontribusi-nama-anda
   ```
3. **Tambahkan fitur atau perbaikan**
4. **Push dan buat Pull Request**
5. **Review kode teman dan berikan feedback**

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

## ✅ Checklist Mahir Git

- [ ] Bisa init repository dan commit pertama
- [ ] Paham working directory, staging, dan repository
- [ ] Bisa buat dan merge branch tanpa konflik
- [ ] Bisa selesaikan merge conflict
- [ ] Bisa push/pull dari GitHub
- [ ] Paham workflow Pull Request
- [ ] Bisa menggunakan .gitignore
- [ ] Bisa reset/revert commit
- [ ] Bisa kolaborasi dalam tim

---

## 🔗 Sumber Belajar Lanjutan

1. **Official Git Documentation**: [git-scm.com](https://git-scm.com/doc)
2. **Interactive Tutorial**: [learngitbranching.js.org](https://learngitbranching.js.org/)
3. **GitHub Guides**: [guides.github.com](https://guides.github.com/)
4. **Git Cheat Sheet**: [education.github.com/git-cheat-sheet-education.pdf](https://education.github.com/git-cheat-sheet-education.pdf)

---

> 💡 **Tips Sukses:** Git adalah skill yang dipelajari dengan praktek. Jangan takut salah - hampir semua masalah Git bisa diperbaiki!
