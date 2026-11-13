# E. Konfigurasi DNS Server BIND9

## 1. Instalasi BIND9

Jalankan perintah berikut untuk memperbarui daftar paket dan menginstal BIND9 beserta utilitas DNS:

```bash
sudo apt update
sudo apt install bind9 bind9-utils dnsutils -y
```

## 2. Menambahkan Konfigurasi Zona DNS

Buka file konfigurasi zona lokal:

```bash
sudo nano /etc/bind/named.conf.local
```

Tambahkan konfigurasi berikut di bagian paling bawah file:

```conf
zone "ukom12tkja-4.sch.id" {
    type master;
    file "/etc/bind/db.ukom12tkja-4";
};
```

Simpan file setelah selesai mengedit.

## 3. Membuat File Zona DNS

Salin file zona bawaan sebagai template:

```bash
sudo cp /etc/bind/db.local /etc/bind/db.ukom12tkja-4
```

Kemudian buka file zona yang baru dibuat:

```bash
sudo nano /etc/bind/db.ukom12tkja-4
```

Sesuaikan isinya menjadi seperti berikut:

```dns
$TTL 604800
@   IN  SOA ns.ukom12tkja-4.sch.id. root.ukom12tkja-4.sch.id. (
        2026102501
        604800
        86400
        2419200
        604800
)

@   IN  NS  ns.ukom12tkja-4.sch.id.
@   IN  A   10.10.4.3
ns  IN  A   10.10.4.3
www IN  A   10.10.4.3
```

Alamat `10.10.4.3` digunakan sebagai contoh IP Ubuntu Server untuk konfigurasi UKOM absen 4. Jika IP server yang dipakai saat praktik berbeda, ganti alamat tersebut dengan IP server yang sebenarnya. Nomor serial zona juga perlu dinaikkan setiap kali file zona diubah.

## 4. Mengatur Opsi BIND9

Buka file opsi BIND9:

```bash
sudo nano /etc/bind/named.conf.options
```

Sesuaikan bagian `options` agar memuat konfigurasi berikut. Jika file sudah memiliki blok `options { ... };`, edit blok yang ada—jangan menambahkan blok `options` kedua.

```conf
options {
    directory "/var/cache/bind";

    listen-on { 127.0.0.1; 10.10.4.3; };
    listen-on-v6 { any; };

    allow-query {
        localhost;
        10.10.4.0/28;
        100.100.4.0/30;
        192.168.4.0/24;
    };

    recursion yes;

    forwarders {
        1.1.1.1;
        8.8.8.8;
    };

    dnssec-validation auto;
};
```

Catatan:
- `listen-on` menentukan alamat IPv4 yang digunakan BIND9 untuk menerima permintaan DNS. Gunakan IP Ubuntu Server yang benar.
- `allow-query` mengizinkan perangkat dari jaringan yang dicantumkan untuk mengirim kueri DNS.
- `forwarders` meneruskan kueri DNS yang tidak dapat dijawab secara lokal ke DNS publik.

## 5. Memeriksa Konfigurasi

Periksa sintaks konfigurasi BIND9:

```bash
sudo named-checkconf
```

Jika tidak ada pesan kesalahan, periksa file zona:

```bash
sudo named-checkzone ukom12tkja-4.sch.id /etc/bind/db.ukom12tkja-4
```

Pastikan hasil pemeriksaan menunjukkan status `OK`.

## 6. Menjalankan dan Memeriksa Layanan BIND9

Aktifkan layanan BIND9 agar berjalan sekarang dan otomatis aktif saat server dinyalakan:

```bash
sudo systemctl enable --now bind9
sudo systemctl restart bind9
sudo systemctl status bind9
```

atau klo gabisa, maka pake:

```bash
sudo systemctl enable --now named
sudo systemctl restart named
sudo systemctl status named
```

Pastikan status layanan menunjukkan `active (running)`.

## 7. Menguji DNS dari Ubuntu Server

Jalankan perintah berikut untuk menguji domain utama dan subdomain `www` melalui DNS lokal:

```bash
dig @127.0.0.1 ukom12tkja-4.sch.id
dig @127.0.0.1 www.ukom12tkja-4.sch.id
```

Periksa bagian jawaban (`ANSWER SECTION`) dan pastikan alamat yang muncul mengarah ke IP Ubuntu Server, yaitu `10.10.4.3` pada contoh ini.

## 8. Menguji DNS dari Komputer Client

Jika domain belum bisa dibuka dari komputer client Windows, bersihkan cache DNS terlebih dahulu:

```cmd
ipconfig /flushdns
```

Kemudian periksa hasil resolusi domain:

```cmd
nslookup ukom12tkja-4.sch.id
nslookup www.ukom12tkja-4.sch.id
```

Pastikan hasilnya mengarah ke IP Ubuntu Server. Komputer client juga harus menggunakan DNS server yang benar, yaitu IP Ubuntu Server (`10.10.25.3` pada contoh ini). Jika DNS yang digunakan client masih mengarah ke alamat lain, hasil `nslookup` tanpa menyebut server DNS tertentu bisa berbeda.

## 9. Kesimpulan

Konfigurasi BIND9 digunakan untuk menerjemahkan nama domain `ukomtkj-25.sch.id` menjadi alamat IP Ubuntu Server. Pemeriksaan konfigurasi, pengujian zona, dan pengujian menggunakan `dig` serta `nslookup` membantu memastikan layanan DNS berjalan dan domain mengarah ke IP server yang benar.
