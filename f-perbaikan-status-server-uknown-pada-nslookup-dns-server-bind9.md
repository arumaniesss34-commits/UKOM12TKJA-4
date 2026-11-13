# PERBAIKAN STATUS SERVER: UNKNOWN PADA NSLOOKUP DNS SERVER BIND9

## A. PENDAHULUAN

Pada saat melakukan pengujian DNS Server menggunakan perintah `nslookup` melalui Command Prompt Windows, terkadang muncul status `Server: Unknown`. Kondisi tersebut menunjukkan bahwa komputer klien belum berhasil memperoleh nama dari alamat IP DNS Server melalui proses Reverse DNS.

Meskipun DNS Server dapat memberikan alamat IP domain dengan benar, nama server tetap dapat ditampilkan sebagai `Unknown` apabila konfigurasi Reverse DNS belum sesuai atau belum dapat diakses.

Oleh karena itu, diperlukan perbaikan pada konfigurasi Forward Zone dan Reverse Zone, serta pengaturan DNS pada komputer klien agar nama DNS Server dapat dikenali dengan benar.

### 1. Tujuan Perbaikan

- Mengatasi status `Server: Unknown` ketika melakukan pengujian menggunakan `nslookup`.
- Memastikan Forward DNS dapat menerjemahkan nama domain menjadi IP Address.
- Memastikan Reverse DNS dapat menerjemahkan IP Address menjadi nama domain.
- Mengarahkan komputer klien agar menggunakan DNS Server yang telah dikonfigurasi.
- Memastikan pengujian `nslookup` dapat dilakukan tanpa menambahkan IP DNS Server secara manual.

### 2. Informasi Konfigurasi

| Keterangan | Konfigurasi |
|---|---|
| Sistem Operasi Server | Ubuntu Server |
| DNS Software | BIND9 |
| Domain | ukom12tkja-4.sch.id |
| IP DNS Server | 10.10.4.3 |
| Network | 10.10.4.0/24 |
| Forward Zone | ukom12tkja-4.sch.id |
| Reverse Zone | 4.10.10.in-addr.arpa |
| File Forward Zone | /etc/bind/db.ukom12tkja-4 |
| File Reverse Zone | /etc/bind/db.10.10.4 |

---

# BAGIAN I – TAHAPAN UTAMA PERBAIKAN

Bagian ini menjelaskan langkah-langkah utama untuk mengatasi status `Server: Unknown` pada pengujian DNS menggunakan BIND9.

## STEP 1 – Memeriksa Konfigurasi Forward Zone dan Reverse Zone

Buka file konfigurasi zona DNS menggunakan perintah:

```bash
sudo nano /etc/bind/named.conf.local
```

Pastikan konfigurasi di dalamnya sudah sesuai:

```conf
zone "ukom12tkja-4.sch.id" {
    type master;
    file "/etc/bind/db.ukom12tkja-4";
};

zone "4.10.10.in-addr.arpa" {
    type master;
    file "/etc/bind/db.10.10.4";
};
```

**Penjelasan:**

File `named.conf.local` digunakan untuk mendaftarkan zona DNS yang dikelola oleh BIND9.

- Forward Zone digunakan untuk menerjemahkan nama domain menjadi alamat IP.
- Reverse Zone digunakan untuk menerjemahkan alamat IP menjadi nama domain.
- `type master` menunjukkan bahwa server bertindak sebagai DNS Master.
- `file` menentukan lokasi file database masing-masing zona.

Pastikan nama zona dan lokasi file sudah sesuai dengan konfigurasi yang digunakan.

Simpan perubahan dengan menekan `CTRL + O`, Enter, kemudian `CTRL + X`.

## STEP 2 – Memperbaiki Konfigurasi Forward Zone

Buka file Forward Zone:

```bash
sudo nano /etc/bind/db.ukom12tkja-4
```

Sesuaikan konfigurasi menjadi:

```dns
$TTL 604800
@   IN  SOA ukom12tkja-4.sch.id. root.ukom12tkja-4.sch.id. (
        2026111201
        604800
        86400
        2419200
        604800
)

@       IN  NS  ukom12tkja-4.sch.id.
@       IN  A   10.10.4.3
www     IN  A   10.10.4.3
```

**Penjelasan:**

Konfigurasi Forward Zone berfungsi menghubungkan nama domain dengan alamat IP DNS Server.

- `SOA` menyimpan informasi utama pengelolaan zona DNS.
- `NS` menentukan DNS Server yang bertanggung jawab terhadap zona.
- `A` digunakan untuk menghubungkan nama domain dengan alamat IPv4.
- `@` mewakili nama domain utama.
- `www` merupakan subdomain yang dapat diakses melalui nama `www.ukom12tkja-4.sch.id`.
- Serial '2026111201' merupakan nomor versi zona. Setiap kali mengubah isi file zona, naikkan serialnya agar perubahan dapat dikenali oleh DNS.

Dengan konfigurasi tersebut, domain utama dan subdomain www akan diarahkan ke alamat IP `10.10.4.3`.

Simpan konfigurasi dengan `CTRL + O`, Enter, kemudian `CTRL + X`.

## STEP 3 – Memperbaiki Konfigurasi Reverse Zone

Buka file Reverse Zone:

```bash
sudo nano /etc/bind/db.10.10.4
```

Sesuaikan konfigurasi menjadi:

```dns
$TTL 604800
@   IN  SOA ukom12tkja-4.sch.id. root.ukom12tkja-4.sch.id. (
        2026111201
        604800
        86400
        2419200
        604800
)

@   IN  NS  ukom12tkja-4.sch.id.
3   IN  PTR ukom12tkja-4.sch.id.
```

**Penjelasan:**

Reverse Zone merupakan bagian terpenting dalam penanganan status `Server: Unknown` karena digunakan untuk menerjemahkan alamat IP DNS Server menjadi nama domain melalui PTR Record.

Perhatikan baris berikut:

```dns
3   IN  PTR ukom12tkja-4.sch.id.
```

Angka `3` merupakan oktet terakhir dari alamat IP `10.10.4.3`.

Sementara itu, nama zona `4.10.10.in-addr.arpa` sudah mewakili tiga oktet sebelumnya. Jika digabungkan, alamat Reverse DNS menjadi:

```text
3.4.10.10.in-addr.arpa
```

PTR Record tersebut akan memberikan nama `ukom12tkja-4.sch.id` ketika komputer melakukan pencarian Reverse DNS terhadap IP `10.10.4.3`.

Simpan konfigurasi dengan `CTRL + O`, Enter, kemudian `CTRL + X`.

**Catatan:** Apabila melakukan perubahan pada file zona, nomor serial SOA perlu dinaikkan agar perubahan dapat dikenali oleh BIND9. Gunakan nomor serial yang sesuai dengan tanggal dan urutan perubahan.

## STEP 4 – Memeriksa Konfigurasi DNS

Setelah Forward Zone dan Reverse Zone diperbaiki, lakukan pemeriksaan sebelum memuat ulang layanan BIND9.

**A. Memeriksa konfigurasi utama BIND9**

```bash
sudo named-checkconf
```

Perintah ini digunakan untuk memeriksa kesalahan sintaks pada konfigurasi BIND9.

Jika tidak terdapat kesalahan, terminal akan kembali ke prompt tanpa menampilkan pesan error.

**B. Memeriksa Forward Zone**

```bash
sudo named-checkzone ukom12tkja-4.sch.id /etc/bind/db.ukom12tkja-4
```

Hasil yang diharapkan:

```text
zone ukom12tkja-4.sch.id/IN: loaded serial 2026111201
OK
```

**C. Memeriksa Reverse Zone**

```bash
sudo named-checkzone 4.10.10.in-addr.arpa /etc/bind/db.10.10.4
```

Hasil yang diharapkan:

```text
zone 4.10.10.in-addr.arpa/IN: loaded serial 2026111201
OK
```

Pastikan kedua pemeriksaan zona menghasilkan keterangan `OK`. Jika masih terdapat kesalahan, perbaiki terlebih dahulu sebelum melanjutkan ke tahap berikutnya.

## STEP 5 – Memuat Ulang Layanan BIND9

Setelah seluruh konfigurasi berhasil diperiksa, jalankan perintah:

```bash
sudo systemctl restart bind9.service
```

Selanjutnya, periksa status layanan:

```bash
sudo systemctl status bind9.service
```

Pastikan muncul keterangan:

```text
Active: active (running)
```

Keterangan tersebut menunjukkan bahwa layanan BIND9 sedang berjalan.

Perintah `restart` digunakan untuk memulai ulang layanan agar konfigurasi DNS yang telah diperbaiki dapat diterapkan.

Tekan `Q` untuk keluar dari tampilan status layanan.

## STEP 6 – Mengatur DNS Server pada Laptop Windows

Setelah konfigurasi pada Ubuntu Server selesai, komputer klien perlu diarahkan untuk menggunakan DNS Server yang telah dibuat.

Langkah-langkahnya:

1. Buka Control Panel.
2. Pilih Network and Internet.
3. Masuk ke Network and Sharing Center.
4. Klik Change adapter settings.
5. Pilih adapter jaringan yang sedang digunakan, misalnya Ethernet atau Wi-Fi.
6. Klik kanan adapter, kemudian pilih Properties.
7. Pilih Internet Protocol Version 4 (TCP/IPv4).
8. Klik Properties.

Pada bagian DNS, pilih:

`Use the following DNS server addresses`

Masukkan konfigurasi berikut:

| Kolom | Alamat |
|---|---|
| Preferred DNS server | 10.10.4.3 |
| Alternate DNS server | Kosongkan dahulu untuk pengujian |

Klik OK, kemudian Close.

**Penjelasan:**

Pengaturan ini bertujuan agar laptop menggunakan DNS Server `10.10.4.3` sebagai DNS utama ketika melakukan permintaan penerjemahan nama domain.

Pastikan alamat IP laptop masih berada pada jaringan yang dapat menjangkau DNS Server tersebut.

## STEP 7 – Membersihkan Cache DNS Windows

Setelah pengaturan DNS diterapkan, buka Command Prompt sebagai Administrator.

Jalankan:

```cmd
ipconfig /flushdns
```

Perintah tersebut berfungsi membersihkan cache DNS yang tersimpan pada Windows agar pengujian selanjutnya tidak menggunakan informasi DNS lama.

Selanjutnya, periksa konfigurasi jaringan menggunakan:

```cmd
ipconfig /all
```

Cari adapter jaringan yang sedang digunakan dan pastikan bagian DNS Servers menunjukkan:

```text
DNS Servers . . . . . . . . . . : 10.10.4.3
```

Jika alamat tersebut sudah muncul, berarti laptop telah menggunakan DNS Server yang ditentukan pada konfigurasi IPv4.

## STEP 8 – Melakukan Pengujian NSLOOKUP

Setelah seluruh konfigurasi selesai, lakukan pengujian menggunakan perintah `nslookup`.

**A. Pengujian dengan menentukan IP DNS Server**

Jalankan:

```cmd
nslookup ukom12tkja-4.sch.id 10.10.4.3
```

Hasil yang diharapkan:

```text
Server:  ukom12tkja-4.sch.id
Address: 10.10.4.3

Name:    ukom12tkja-4.sch.id
Address: 10.10.4.3
```

Pengujian ini memastikan DNS Server dapat memberikan jawaban terhadap permintaan domain dan nama server dapat dikenali melalui Reverse DNS.

**B. Pengujian tanpa menambahkan IP DNS Server**

Setelah pengujian pertama berhasil, jalankan:

```cmd
nslookup ukom12tkja-4.sch.id
```

Hasil yang diharapkan:

```text
Server:  ukom12tkja-4.sch.id
Address: 10.10.4.3

Name:    ukom12tkja-4.sch.id
Address: 10.10.4.3
```

**Penjelasan:**

Pengujian pertama menggunakan IP DNS Server secara langsung, sedangkan pengujian kedua menggunakan DNS Server yang telah diatur pada adapter jaringan Windows.

Jika kedua pengujian berhasil dan bagian `Server` menampilkan nama domain, berarti masalah `Server: Unknown` telah berhasil diatasi.

---

# BAGIAN II – TROUBLESHOOTING / PEMECAHAN MASALAH

Bagian ini digunakan apabila setelah mengikuti seluruh tahapan perbaikan, hasil pengujian masih belum sesuai atau muncul pesan kesalahan.

Lakukan pemeriksaan berdasarkan gejala yang ditemukan agar proses perbaikan lebih terarah.

## TROUBLESHOOTING 1 – Server Masih Menampilkan Unknown

**Gejala:**

```text
Server: Unknown
Address: 10.10.4.3
```

**Kemungkinan penyebab:**

- PTR Record belum dikonfigurasi dengan benar.
- Reverse Zone belum berhasil dimuat.
- Nama domain pada PTR Record tidak sesuai.
- DNS Server belum memberikan jawaban Reverse DNS yang benar.

**Langkah pemeriksaan:**

Periksa Reverse Zone:

```bash
sudo named-checkzone 4.10.10.in-addr.arpa /etc/bind/db.10.10.4
```

Kemudian lakukan pengujian:

```bash
dig @127.0.0.1 -x 10.10.4.3
```

Pastikan hasil PTR mengarah ke:

```text
ukom12tkja-4.sch.id.
```

Jika hasilnya belum sesuai, buka kembali file Reverse Zone:

```bash
sudo nano /etc/bind/db.10.10.4
```

Pastikan terdapat konfigurasi:

```dns
3 IN PTR ukom12tkja-4.sch.id.
```

Setelah diperbaiki, naikkan serial SOA, periksa zona kembali, kemudian restart BIND9.

## TROUBLESHOOTING 2 – Forward DNS Berhasil, tetapi Reverse DNS Gagal

**Gejala:**

Forward DNS dapat menerjemahkan domain menjadi IP Address, tetapi nama DNS Server belum berhasil dikenali.

**Langkah pemeriksaan:**

Jalankan perintah berikut pada Windows CMD:

```cmd
nslookup -type=PTR 3.4.10.10.in-addr.arpa 10.10.4.3
```

Perintah tersebut digunakan untuk meminta informasi PTR Record secara langsung kepada DNS Server.

Jika hasilnya belum menunjukkan nama domain, periksa kembali file Reverse Zone pada Ubuntu Server.

Pastikan konfigurasi PTR Record sudah sesuai dengan IP DNS Server.

## TROUBLESHOOTING 3 – NSLOOKUP Mengalami Request Timed Out

**Gejala:**

```text
DNS request timed out.
timeout was 2 seconds.
```

**Kemungkinan penyebab:**

- Layanan BIND9 tidak berjalan.
- Port 53 belum menerima permintaan DNS.
- Laptop tidak dapat menjangkau DNS Server.
- Firewall memblokir koneksi DNS.

**Langkah pemeriksaan:**

Periksa layanan BIND9:

```bash
sudo systemctl status bind9.service
```

Periksa port DNS:

```bash
sudo ss -tulpn | grep ':53'
```

Periksa konektivitas dari Windows:

```cmd
ping 10.10.4.3
```

Jika layanan tidak berjalan, periksa pesan kesalahannya sebelum melakukan perbaikan.

Perlu diperhatikan bahwa ping yang gagal belum tentu menunjukkan DNS tidak dapat digunakan karena ICMP dapat diblokir. Lakukan juga pengujian DNS langsung.

## TROUBLESHOOTING 4 – NSLOOKUP Masih Menggunakan DNS Server Lain

**Gejala:**

Ketika menjalankan `nslookup` tanpa tambahan IP, bagian Server masih menunjukkan DNS Server lain.

**Langkah pemeriksaan:**

Jalankan:

```cmd
ipconfig /all
```

Cari bagian DNS Servers pada adapter jaringan yang sedang digunakan.

Pastikan alamat DNS yang muncul adalah:

```text
10.10.4.3
```

Jika belum sesuai, periksa kembali pengaturan IPv4 pada adapter jaringan Windows.

Apabila DNS diperoleh melalui DHCP, pastikan konfigurasi DHCP tidak memberikan DNS Server lain yang menggantikan pengaturan tersebut.

## TROUBLESHOOTING 5 – BIND9 Gagal Berjalan Setelah Konfigurasi Diubah

**Gejala:**

```text
Active: failed
```

**Langkah pemeriksaan:**

Periksa konfigurasi utama:

```bash
sudo named-checkconf
```

Periksa Forward Zone:

```bash
sudo named-checkzone ukom12tkja-4.sch.id /etc/bind/db.ukom12tkja-4
```

Periksa Reverse Zone:

```bash
sudo named-checkzone 4.10.10.in-addr.arpa /etc/bind/db.10.10.4
```

Jika ditemukan kesalahan, perbaiki file konfigurasi yang bermasalah.

Untuk melihat informasi error layanan BIND9, jalankan:

```bash
sudo journalctl -u bind9.service -n 30 --no-pager
```

Setelah seluruh kesalahan diperbaiki, jalankan kembali:

```bash
sudo systemctl restart bind9.service
```

Kemudian periksa status layanan.

## TROUBLESHOOTING 6 – DNS Server Tidak Menerima Permintaan dari Laptop

**Gejala:**

DNS Server dapat diuji secara lokal, tetapi tidak dapat memberikan jawaban ketika diakses dari laptop.

**Langkah pemeriksaan:**

Periksa port DNS:

```bash
sudo ss -tulpn | grep ':53'
```

Pastikan BIND9 mendengarkan pada alamat jaringan yang dapat dijangkau laptop, bukan hanya pada alamat localhost.

Periksa konfigurasi:

```bash
sudo nano /etc/bind/named.conf.options
```

Pastikan pengaturan `listen-on` dan `allow-query` mengizinkan permintaan dari jaringan klien.

Jika menggunakan UFW, periksa status firewall:

```bash
sudo ufw status
```

Jika UFW aktif dan belum mengizinkan DNS dari jaringan lab, tambahkan aturan:

```bash
sudo ufw allow from 10.10.4.0/24 to any port 53 proto udp
sudo ufw allow from 10.10.4.0/24 to any port 53 proto tcp
```

Setelah itu, lakukan pengujian kembali dari laptop.

---

# BAGIAN III – CHECKLIST PENGUJIAN AKHIR

Sebelum menyatakan konfigurasi berhasil, pastikan seluruh poin berikut telah terpenuhi:

- [ ] Forward Zone sudah terdaftar pada named.conf.local.
- [ ] Reverse Zone sudah terdaftar pada named.conf.local.
- [ ] A Record mengarah ke IP 10.10.4.3.
- [ ] PTR Record mengarah ke ukom12tkj-4.sch.id.
- [ ] named-checkconf tidak menghasilkan error.
- [ ] Pemeriksaan Forward Zone menghasilkan OK.
- [ ] Pemeriksaan Reverse Zone menghasilkan OK.
- [ ] Layanan BIND9 berstatus active (running).
- [ ] Laptop dapat menjangkau DNS Server 10.10.4.3.
- [ ] DNS Server pada Windows menunjukkan IP 10.10.4.3.
- [ ] Pengujian nslookup dengan tambahan IP berhasil.
- [ ] Pengujian nslookup tanpa tambahan IP berhasil.
- [ ] Bagian Server menampilkan nama domain, bukan Unknown.

---

# KESIMPULAN

Permasalahan `Server: Unknown` pada pengujian DNS menggunakan perintah `nslookup` dapat ditangani dengan memperbaiki konfigurasi Reverse DNS, khususnya melalui penambahan PTR Record yang menghubungkan alamat IP DNS Server dengan nama domain.

Selain itu, konfigurasi Forward DNS harus dipastikan berjalan dengan benar agar nama domain dapat diterjemahkan menjadi IP Address. Pengaturan DNS pada komputer klien juga perlu diarahkan ke alamat IP DNS Server yang telah dikonfigurasi.

Setelah seluruh tahapan perbaikan berhasil dilakukan, pengujian dapat dilanjutkan menggunakan perintah:

```cmd
nslookup ukom12tkja-4.sch.id
```

Hasil yang diharapkan adalah bagian `Server` menampilkan nama domain DNS Server, bukan lagi `Unknown`, serta bagian `Address` menunjukkan alamat IP `10.10.4.3`.

Dengan demikian, konfigurasi Forward DNS dan Reverse DNS telah berjalan sesuai kebutuhan pengujian.

---

# REFERENSI

1. Ubuntu Server Documentation – Domain Name Service (DNS): https://documentation.ubuntu.com/server/how-to/networking/configure-dns/
2. BIND 9 Administrator Reference Manual: https://bind9.readthedocs.io/
3. Microsoft Learn – Nslookup: https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/nslookup
