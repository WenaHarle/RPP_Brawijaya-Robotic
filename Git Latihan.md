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


| No | Perintah Git                  | Fungsi                                            | Contoh Penggunaan                                          |
| -- | ----------------------------- | ------------------------------------------------- | ---------------------------------------------------------- |
| 1  | `git init`                    | Membuat repository Git lokal baru                 | `git init`                                                 |
| 2  | `git clone <url>`             | Mengunduh repo dari remote (misal GitHub)         | `git clone https://github.com/user/projek.git`             |
| 3  | `git status`                  | Melihat status file (modified, staged, untracked) | `git status`                                               |
| 4  | `git add <file>`              | Menambahkan file ke staging area                  | `git add index.html`                                       |
| 5  | `git add .`                   | Menambahkan semua file ke staging area            | `git add .`                                                |
| 6  | `git commit -m "pesan"`       | Menyimpan perubahan dengan pesan                  | `git commit -m "Tambah fitur login"`                       |
| 7  | `git log`                     | Melihat riwayat commit                            | `git log`                                                  |
| 8  | `git diff`                    | Melihat perbedaan antar versi file                | `git diff`                                                 |
| 9  | `git branch`                  | Melihat semua branch                              | `git branch`                                               |
| 10 | `git branch <nama>`           | Membuat branch baru                               | `git branch fitur-login`                                   |
| 11 | `git checkout <nama>`         | Pindah ke branch tertentu                         | `git checkout fitur-login`                                 |
| 12 | `git switch <nama>`           | Alternatif modern untuk `checkout`                | `git switch main`                                          |
| 13 | `git merge <branch>`          | Menggabungkan branch ke branch aktif              | `git merge fitur-login`                                    |
| 14 | `git remote add origin <url>` | Menyambungkan repo lokal ke GitHub                | `git remote add origin https://github.com/user/projek.git` |
| 15 | `git push -u origin <branch>` | Mengirim commit ke remote pertama kali            | `git push -u origin main`                                  |
| 16 | `git push`                    | Mengirim perubahan ke remote                      | `git push`                                                 |
| 17 | `git pull`                    | Mengambil dan menggabungkan dari remote           | `git pull`                                                 |
| 18 | `git fetch`                   | Mengambil update dari remote tanpa merge          | `git fetch`                                                |
| 19 | `git reset --hard <commit>`   | Mengembalikan repo ke commit tertentu             | `git reset --hard a1b2c3d`                                 |
| 20 | `git rm <file>`               | Menghapus file dan mencatatnya di Git             | `git rm data.txt`                                          |
| 21 | `git restore <file>`          | Mengembalikan file ke versi terakhir commit       | `git restore index.html`                                   |
| 22 | `git stash`                   | Menyimpan perubahan sementara                     | `git stash`                                                |
| 23 | `git stash pop`               | Mengambil kembali perubahan dari stash            | `git stash pop`                                            |
| 24 | `git tag <nama>`              | Memberi tag pada commit                           | `git tag v1.0.0`                                           |
| 25 | `git config`                  | Mengatur konfigurasi Git (nama/email dll.)        | `git config --global user.name "Budi"`                     |

