# B. Instalasi Web Server Apache dan PHP

Bagian ini menjelaskan instalasi Apache sebagai web server dan PHP agar Ubuntu Server dapat menampilkan halaman website serta mengambil data dari database MariaDB yang dibuat pada Bagian A.

## 1. Menginstal Apache dan PHP

Jalankan perintah berikut pada terminal Ubuntu Server:

```bash
sudo apt install apache2 php libapache2-mod-php php-mysql -y
```

Perintah tersebut menginstal Apache, PHP, modul PHP untuk Apache, dan dukungan koneksi PHP ke MySQL/MariaDB.

## 2. Mengaktifkan dan memeriksa layanan Apache

Jalankan perintah berikut:

```bash
sudo systemctl enable --now apache2
sudo systemctl status apache2
```

Pastikan status layanan menunjukkan **`active (running)`**. Tekan `q` untuk keluar dari tampilan status.

## 3. Membuat halaman utama website

Buka file halaman utama Apache menggunakan perintah:

```bash
sudo nano /var/www/html/index.html
```

File `index.html` digunakan untuk menampilkan landing page website. Isi HTML akan disiapkan pada file terpisah, sehingga pada bagian ini cukup membuka atau mengedit file tersebut. Simpan perubahan dengan `Ctrl + O`, tekan `Enter`, lalu keluar dengan `Ctrl + X`.

## 4. Membuat halaman yang menampilkan data database

Buat file PHP:

```bash
sudo nano /var/www/html/database.php
```

Masukkan kode berikut. Kode ini menghubungkan PHP ke database `ukom4`, membaca data dari tabel `informasi`, lalu menampilkan hasilnya di browser.

```php
<?php
$host = "localhost";
$user = "ukom4";
$password = "GANTI_DENGAN_PASSWORD_DATABASE";
$database = "ukom4";

$conn = new mysqli($host, $user, $password, $database);

if ($conn->connect_error) {
    http_response_code(500);
    die("Koneksi database gagal.");
}

$conn->set_charset("utf8mb4");

echo "<h1>Koneksi Database Berhasil</h1>";
echo "<h2>Data Informasi UKOM TKJ</h2>";

$sql = "SELECT id, nama, keterangan, created_at FROM informasi";
$result = $conn->query($sql);

if ($result && $result->num_rows > 0) {
    while ($row = $result->fetch_assoc()) {
        echo "<hr>";
        echo "ID: " . htmlspecialchars((string)$row["id"]) . "<br>";
        echo "Nama: " . htmlspecialchars($row["nama"]) . "<br>";
        echo "Keterangan: " .
             htmlspecialchars($row["keterangan"]) . "<br>";
        echo "Dibuat: " .
             htmlspecialchars($row["created_at"]) . "<br>";
    }
} else {
    echo "Belum ada data informasi.";
}

$conn->close();
?>
```

**Penting:** ganti `GANTI_DENGAN_PASSWORD_DATABASE` dengan password user MariaDB yang benar pada server. Password pada contoh sengaja ditulis sebagai placeholder agar tidak menyimpan password asli di dokumentasi.

Simpan file dengan `Ctrl + O`, tekan `Enter`, lalu keluar dengan `Ctrl + X`.

## 5. Memulai ulang Apache

Setelah file selesai dibuat, jalankan:

```bash
sudo systemctl restart apache2
```

## 6. Pengujian melalui browser client

Dari browser client yang dapat menjangkau Ubuntu Server, buka alamat berikut:

- `http://ukom12tkja-4.sch.id/` — untuk menguji landing page `index.html` melalui domain.
- `http://ukom12tkja-4.sch.id/database.php` — untuk menguji koneksi PHP ke MariaDB dan menampilkan data dari tabel `informasi`.
- Jika DNS domain belum siap, akses sementara melalui IP server: `http://10.10.4.3/` dan `http://10.10.4.3/database.php`.

Jika halaman landing page tampil dan halaman `database.php` menunjukkan pesan **“Koneksi Database Berhasil”** beserta data informasi, maka Apache, PHP, dan koneksi ke database telah berjalan.

> **Catatan IP:** catatan awalmu menyebut alamat `10.10.4.2`, tetapi konfigurasi Bagian A menetapkan Ubuntu Server pada `10.10.4.3/28` dan `10.10.4.2` sebagai IP laptop pengelola. Karena itu, contoh URL di dokumen ini menggunakan `10.10.4.3`. Jika IP Ubuntu Server di praktik sebenarnya berbeda, ganti URL sesuai IP server yang aktif. Sebelum dipublikasikan ke repository Public, pastikan tidak ada password asli yang tersimpan dalam kode.
