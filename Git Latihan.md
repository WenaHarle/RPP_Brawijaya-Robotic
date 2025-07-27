## 📘 Materi Dasar Git + Percobaan

### 🎯 Tujuan Pembelajaran

Peserta mampu:

* Memahami konsep dasar version control.
* Menggunakan perintah Git dasar untuk membuat dan mengelola repository.
* Melakukan commit, melihat riwayat perubahan, dan berpindah antar versi.
* Bekerja dengan GitHub (opsional).

---

## 🧠 Konsep Dasar Git

* **Version Control**: Sistem untuk mencatat perubahan pada file.
* **Git**: Version control system yang menyimpan snapshot file.
* **Repository (repo)**: Folder proyek yang dilacak Git.
* **Commit**: Mencatat perubahan dalam repo.
* **Branch**: Versi paralel dari kode.
* **Merge**: Menggabungkan perubahan antar branch.
* **Remote**: Repo Git yang berada di server (misal: GitHub).

---

## 🛠️ Setup Awal

### Persyaratan:

* Sudah menginstal **Git**.
* Terminal / Command Line aktif.
* (Opsional) Akun GitHub untuk percobaan online.

### Konfigurasi Awal (hanya 1x):

```bash
git config --global user.name "Nama Kamu"
git config --global user.email "email@example.com"
```

---

## 🧪 Percobaan Git Dasar

### 🔬 Percobaan 1: Membuat Repository Lokal

```bash
mkdir belajar-git
cd belajar-git
git init
```

> ✅ Cek dengan `ls -a`, akan ada folder `.git`.

---

### 🔬 Percobaan 2: Menambahkan dan Commit File

```bash
echo "Hello Git" > hello.txt
git status          # melihat status
git add hello.txt   # stage file
git commit -m "Initial commit: tambah hello.txt"
```

---

### 🔬 Percobaan 3: Melihat Riwayat Perubahan

```bash
git log
```

> Tekan `q` untuk keluar dari log view.

---

### 🔬 Percobaan 4: Mengubah File dan Commit Lagi

```bash
echo "Baris kedua" >> hello.txt
git add hello.txt
git commit -m "Tambah baris kedua"
```

---

### 🔬 Percobaan 5: Melihat Perbedaan (Diff)

```bash
git diff HEAD~1 HEAD
```

---

### 🔬 Percobaan 6: Membuat dan Berpindah Branch

```bash
git branch fitur-baru
git checkout fitur-baru
echo "Fitur baru" > fitur.txt
git add fitur.txt
git commit -m "Tambah fitur.txt"
```

---

### 🔬 Percobaan 7: Merge Branch ke Main

```bash
git checkout main
git merge fitur-baru
```

---

### 🔬 Percobaan 8: Menyambungkan ke GitHub (Opsional)

```bash
git remote add origin https://github.com/username/belajar-git.git
git push -u origin main
```

---

## 📝 Latihan Mandiri

1. Buat repo `latihan-git`.
2. Tambah file README.md.
3. Buat 2 branch berbeda: `desain`, `fitur-x`.
4. Tambah file berbeda di tiap branch.
5. Merge ke main.
