# C. Konfigurasi VirtualHost Apache

Bagian ini digunakan untuk menghubungkan konfigurasi website dengan domain yang ditentukan. VirtualHost mengatur domain yang dilayani Apache dan direktori website yang akan ditampilkan.

## 1. Membuat file konfigurasi VirtualHost

Jalankan perintah berikut:

```bash
sudo nano /etc/apache2/sites-available/ukom12tkja-4.sch.id.conf
```

Masukkan konfigurasi VirtualHost berikut ke dalam file tersebut:

```apache
<VirtualHost *:80>
    ServerName ukom12tkja-4.sch.id
    ServerAlias www.ukom12tkja-4.sch.id

    DocumentRoot /var/www/html
    DirectoryIndex index.html index.php

    <Directory /var/www/html>
        Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/ukom-error.log
    CustomLog ${APACHE_LOG_DIR}/ukom-access.log combined
</VirtualHost>
```

Simpan file dengan `Ctrl + O`, tekan `Enter`, lalu keluar dengan `Ctrl + X`.

## 2. Mengaktifkan VirtualHost

Jalankan perintah berikut untuk mengaktifkan konfigurasi website:

```bash
sudo a2ensite ukom12tkja-4.sch.id.conf
```

## 3. Memeriksa konfigurasi Apache

Periksa apakah konfigurasi Apache memiliki kesalahan sintaks:

```bash
sudo apache2ctl configtest
```

Jika konfigurasi benar, hasilnya akan menampilkan:

```text
Syntax OK
```

Jika muncul kesalahan, periksa kembali file konfigurasi sebelum melanjutkan.

## 4. Memuat ulang Apache

Jika pemeriksaan berhasil, muat ulang Apache agar konfigurasi baru digunakan:

```bash
sudo systemctl reload apache2
```

## 5. Pengujian

Pastikan domain `ukom12tkja-4.sch.id` diarahkan ke IP Ubuntu Server melalui konfigurasi DNS yang sesuai. Setelah itu, akses domain melalui browser client yang dapat menjangkau server.

**Catatan:** domain pada bagian ini mengikuti catatan yang kamu kirim, yaitu `ukom12tkja-4.sch.id`. Pastikan penulisan domain sama dengan konfigurasi DNS dan ketentuan penguji. Jika IP server atau nama domain yang ditetapkan berbeda, sesuaikan sebelum pengujian.
