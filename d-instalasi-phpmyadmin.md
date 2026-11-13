# D. Instalasi phpMyAdmin

Bagian ini menjelaskan instalasi phpMyAdmin, yaitu antarmuka berbasis web untuk membantu mengelola database MariaDB melalui browser.

## 1. Memperbarui daftar paket dan menginstal phpMyAdmin

Jalankan perintah berikut pada Ubuntu Server:

```bash
sudo apt update
sudo apt install phpmyadmin php-mysql -y
```

Ikuti pilihan konfigurasi yang muncul selama instalasi. Jika installer meminta web server, pilih Apache2 bila opsi tersebut tersedia.

## 2. Memeriksa konfigurasi Apache phpMyAdmin

Periksa apakah file konfigurasi phpMyAdmin tersedia di Apache:

```bash
ls -l /etc/apache2/conf-available/ | grep -i phpMyAdmin
```

Jika konfigurasi `phpmyadmin` tersedia, aktifkan dengan perintah:

```bash
sudo a2enconf phpmyadmin
sudo apache2ctl configtest
sudo systemctl restart apache2
```

Pastikan `apache2ctl configtest` menampilkan `Syntax OK` sebelum memulai ulang Apache.

## 3. Pengujian melalui browser

Buka browser pada client yang dapat menjangkau Ubuntu Server, lalu akses:

```text
http://10.10.4.3/phpmyadmin
```

Jika halaman phpMyAdmin belum dapat dibuka, lanjutkan pemeriksaan berikut.

## 4. Pemeriksaan jika phpMyAdmin tidak bisa diakses

Periksa apakah paket phpMyAdmin telah terpasang:

```bash
dpkg -l | grep phpmyadmin
```

Cari lokasi file konfigurasi Apache yang disediakan oleh paket:

```bash
dpkg -L phpmyadmin | grep -E 'apache.conf|phpmyadmin.conf'
```

Jika hasilnya menunjukkan file `/etc/phpmyadmin/apache.conf`, buat tautan konfigurasi agar dapat diaktifkan oleh Apache:

```bash
sudo ln -s /etc/phpmyadmin/apache.conf /etc/apache2/conf-available/phpmyadmin.conf
```

Kemudian aktifkan konfigurasi dan periksa sintaks Apache:

```bash
sudo a2enconf phpmyadmin
sudo apache2ctl configtest
sudo systemctl restart apache2
```

Jika perintah `ln -s` memberi tahu bahwa file tujuan sudah ada, jangan jalankan perintah itu berulang kali; periksa konfigurasi yang sudah tersedia terlebih dahulu.

## 5. Pengujian ulang

Coba akses kembali melalui browser menggunakan IP server:

```text
http://10.10.4.3/phpmyadmin
```

Jika DNS domain sudah diarahkan dengan benar ke Ubuntu Server, phpMyAdmin juga dapat diuji melalui domain:

```text
http://ukom12tkja-4.sch.id/phpmyadmin
```

## Catatan

- Pada catatan awal terdapat URL IP `10.10.4.2`, tetapi dokumentasi sebelumnya menetapkan IP Ubuntu Server `10.10.4.3`. Bagian ini menggunakan `10.10.4.3`; sesuaikan jika alamat server yang sebenarnya berbeda.
- Domain diseragamkan menjadi `ukom12tkja-4.sch.id` sesuai koreksi sebelumnya.
- Login phpMyAdmin menggunakan akun MariaDB yang memiliki akses ke database. Jangan menaruh password asli dalam dokumentasi yang akan dipublikasikan ke repository Public.
