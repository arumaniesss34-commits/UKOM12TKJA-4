# A. Instalasi dan Konfigurasi Database MariaDB

Bagian ini menjelaskan langkah instalasi MariaDB pada Ubuntu Server, membuat database dan user, menambahkan tabel serta data informasi, lalu menguji apakah database dapat diakses menggunakan user yang sudah dibuat.

## 1. Memperbarui daftar paket

Jalankan perintah berikut untuk memperbarui daftar paket Ubuntu:

```bash
sudo apt update
```

## 2. Menginstal MariaDB

Instal MariaDB Server dan MariaDB Client dengan perintah:

```bash
sudo apt install mariadb-server mariadb-client -y
```

## 3. Mengaktifkan dan memeriksa layanan MariaDB

Jalankan perintah berikut agar MariaDB aktif otomatis saat server dinyalakan dan mulai berjalan sekarang:

```bash
sudo systemctl enable --now mariadb
sudo systemctl status mariadb
```

Pastikan status layanan menunjukkan **`active (running)`**. Tekan `q` untuk keluar dari tampilan status.

## 4. Menjalankan pengamanan awal MariaDB

Jalankan perintah:

```bash
sudo mariadb-secure-installation
```

Ikuti pertanyaan yang muncul sesuai kebutuhan konfigurasi server. Setelah selesai, masuk ke konsol MariaDB:

```bash
sudo mariadb
```

## 5. Membuat database dan user

Di dalam konsol MariaDB, jalankan perintah berikut untuk membuat database `ukom4`:

```sql
CREATE DATABASE ukom4
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_ci;
```

Buat user database lokal:

```sql
CREATE USER 'ukom4'@'localhost'
IDENTIFIED BY 'UkomDB@2026';
```

Berikan hak akses kepada user tersebut untuk mengelola database `ukom4`:

```sql
GRANT ALL PRIVILEGES ON ukom4.* TO 'ukom4'@'localhost';

FLUSH PRIVILEGES;
```

Periksa daftar database:

```sql
SHOW DATABASES;
```

## 6. Memilih database dan membuat tabel

Pilih database yang baru dibuat:

```sql
USE ukom4;
```

Kemudian buat tabel `informasi`:

```sql
CREATE TABLE informasi (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nama VARCHAR(100) NOT NULL,
    keterangan TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

Periksa apakah tabel sudah dibuat:

```sql
SHOW TABLES;
```

## 7. Memasukkan data ke tabel

Tambahkan data contoh ke tabel `informasi`:

```sql
INSERT INTO informasi (nama, keterangan)
VALUES
('UKOM TKJ', 'Web Server dan Database Server'),
('Absen 4', 'Konfigurasi jaringan UKOM'),
('MikroTik', 'Gateway dan HotSpot'),
('Server', 'Ubuntu Server (ip ubuntu)');
```

Untuk melihat data yang tersimpan, jalankan:

```sql
SELECT * FROM informasi;
```

Setelah selesai, keluar dari konsol MariaDB:

```sql
EXIT;
```

## 8. Menguji login menggunakan user database

Di terminal Ubuntu, jalankan:

```bash
mariadb -u ukom4 -p ukom4
```

Masukkan password yang ditetapkan saat membuat user. Pada contoh konfigurasi ini, password-nya adalah `UkomDB@2026`. Saat mengetik password, karakter biasanya tidak ditampilkan di terminal.

Setelah berhasil masuk ke konsol MariaDB, jalankan:

```sql
SELECT * FROM informasi;
```

Jika data tampil, berarti database, tabel, data, dan akses user berhasil dikonfigurasi. Keluar dengan perintah:

```sql
EXIT;
```

## Catatan penting

- Pastikan nama database konsisten menggunakan `ukom4`. Pada catatan awal terdapat `USE ukom4;`; langkah tersebut perlu menggunakan `USE ukom4;` agar sesuai dengan database yang dibuat.
- Ganti teks `Ubuntu Server (ip ubuntu)` dengan alamat IP Ubuntu Server yang benar jika ingin menyimpan IP server pada data tabel.
- Password di atas mengikuti catatan konfigurasi yang diberikan. Jika repository GitHub akan dibuat **Public**, jangan cantumkan password asli dalam dokumentasi. Ganti dengan placeholder seperti `<PASSWORD_DATABASE>` sebelum dipublikasikan.
