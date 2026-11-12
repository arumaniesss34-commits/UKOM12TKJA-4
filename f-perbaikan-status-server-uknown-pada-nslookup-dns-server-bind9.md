PERBAIKAN STATUS SERVER: UNKNOWN PADA NSLOOKUP DNS SERVER BIND9
BAGIAN A – TAHAPAN UTAMA PERBAIKAN
Bagian ini merupakan tahapan inti untuk memperbaiki status Server: Unknown ketika melakukan pengujian DNS menggunakan perintah nslookup pada Windows.
Step 1 – Memeriksa konfigurasi Forward Zone dan Reverse Zone
Buka konfigurasi zona DNS:
sudo nano /etc/bind/named.conf.local


Pastikan konfigurasi sudah sesuai:
zone "ukom12tkj-4.sch.id" {
    type master;
    file "/etc/bind/db.ukom12tkj-4";
};

zone "4.10.10.in-addr.arpa" {
    type master;
    file "/etc/bind/db.10.10.4";
};


Fungsi:

Konfigurasi ini mendaftarkan dua zona DNS yang akan digunakan oleh BIND9.
- Forward Zone digunakan untuk menerjemahkan nama domain menjadi IP Address.
- Reverse Zone digunakan untuk menerjemahkan IP Address menjadi nama domain.
Pastikan nama zona dan lokasi file sesuai dengan konfigurasi DNS Server yang digunakan.
Step 2 – Memperbaiki Forward Zone
Buka file Forward Zone:
sudo nano /etc/bind/db.ukom12tkj-4


Pastikan isinya:
$TTL 604800
@   IN  SOA ukom12tkj-4.sch.id. root.ukom12tkj-4.sch.id. (
        2026111201
        604800
        86400
        2419200
        604800
)

@       IN  NS  ukom12tkj-4.sch.id.
@       IN  A   10.10.4.3
www     IN  A   10.10.4.3


Fungsi:

Forward Zone memastikan domain ukom12tkj-4.sch.id dan subdomain www.ukom12tkj-4.sch.id mengarah ke IP Address 10.10.4.3.

Dengan demikian, DNS Server dapat menerjemahkan nama domain menjadi alamat IP yang sesuai.
Step 3 – Memperbaiki Reverse Zone
Buka file Reverse Zone:
sudo nano /etc/bind/db.10.10.4


Pastikan isinya:
$TTL 604800
@   IN  SOA ukom12tkj-4.sch.id. root.ukom12tkj-4.sch.id. (
        2026111201
        604800
        86400
        2419200
        604800
)

@   IN  NS  ukom12tkj-4.sch.id.
3   IN  PTR ukom12tkj-4.sch.id.


Fungsi:

Reverse Zone digunakan untuk menghubungkan alamat IP DNS Server dengan nama domainnya melalui PTR Record.

Baris berikut merupakan bagian terpenting:
3   IN  PTR ukom12tkj-4.sch.id.


Angka 3 merupakan oktet terakhir dari IP 10.10.4.3.

Ketika Windows meminta informasi nama dari IP DNS Server, PTR Record tersebut akan memberikan jawaban berupa ukom12tkj-4.sch.id.

Catatan: Jika kamu mengubah isi zona, naikkan nomor serial SOA agar perubahan dapat dikenali BIND9. Gunakan serial yang sama sekali lagi pada kedua file jika keduanya diperbarui bersamaan.
Step 4 – Memeriksa konfigurasi DNS sebelum diterapkan
Setelah kedua file zona selesai diperbaiki, lakukan pemeriksaan.

Periksa konfigurasi utama:
sudo named-checkconf


Periksa Forward Zone:
sudo named-checkzone ukom12tkj-4.sch.id /etc/bind/db.ukom12tkj-4


Periksa Reverse Zone:
sudo named-checkzone 4.10.10.in-addr.arpa /etc/bind/db.10.10.4


Hasil yang diharapkan pada kedua pemeriksaan zona:
loaded serial 2026111201
OK


Jika semua pemeriksaan berhasil, lanjutkan ke tahap berikutnya.
Step 5 – Memuat ulang layanan BIND9
Jalankan:
sudo systemctl restart bind9.service


Kemudian periksa status layanan:
sudo systemctl status bind9.service


Pastikan muncul:
Active: active (running)


Artinya layanan BIND9 sudah berjalan dan siap menerima permintaan DNS.
Step 6 – Mengatur DNS Server pada laptop Windows
Tahapan ini memastikan laptop menggunakan DNS Server milikmu, bukan DNS lain dari router atau jaringan.
1. Buka Control Panel.
2. Pilih Network and Internet.
3. Masuk ke Network and Sharing Center.
4. Klik Change adapter settings.
5. Klik kanan adapter jaringan yang sedang digunakan.
6. Pilih Properties.
7. Pilih Internet Protocol Version 4 (TCP/IPv4).
8. Klik Properties.
Pada bagian DNS, pilih:

Use the following DNS server addresses

Masukkan:
Kolom	Alamat
Preferred DNS server	10.10.4.3
Alternate DNS server	Kosongkan dahulu

Klik OK, kemudian Close.

Pastikan IP Address dan koneksi laptop masih berada pada jaringan yang dapat menjangkau DNS Server 10.10.4.3.
Step 7 – Membersihkan cache DNS Windows
Buka CMD sebagai Administrator.

Jalankan:
ipconfig /flushdns


Perintah ini membersihkan cache DNS Windows agar pengujian berikutnya tidak menggunakan informasi DNS lama.

Selanjutnya, periksa DNS yang digunakan laptop:
ipconfig /all


Cari adapter yang sedang aktif dan pastikan bagian DNS Servers menunjukkan:
DNS Servers . . . . . . . . . . : 10.10.4.3


Step 8 – Melakukan pengujian akhir NSLOOKUP
Sekarang lakukan pengujian pertama dengan menentukan IP DNS Server secara langsung:
nslookup ukom12tkj-4.sch.id 10.10.4.3


Hasil yang diharapkan:
Server:  ukom12tkj-4.sch.id
Address: 10.10.4.3

Name:    ukom12tkj-4.sch.id
Address: 10.10.4.3


Jika berhasil, lanjutkan pengujian kedua tanpa menambahkan IP:
nslookup ukom12tkj-4.sch.id


Hasil yang diharapkan tetap sama:
Server:  ukom12tkj-4.sch.id
Address: 10.10.4.3

Name:    ukom12tkj-4.sch.id
Address: 10.10.4.3


Perbaikan berhasil
Jika kedua pengujian menampilkan nama server dan IP yang sesuai, berarti masalah Server: Unknown telah berhasil diatasi.
