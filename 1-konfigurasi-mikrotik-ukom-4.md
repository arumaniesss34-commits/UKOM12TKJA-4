# KONFIGURASI ROUTER MIKROTIK – UKOM JUNIOR NETWORK ADMINISTRATOR PAKET 3

## 1. Tujuan Konfigurasi

Melakukan konfigurasi router MikroTik sebagai gateway internet sekaligus perangkat distribusi jaringan untuk client kabel, server, dan client wireless menggunakan fasilitas Hotspot.

Konfigurasi dilakukan menggunakan satu perangkat router MikroTik dengan pembagian interface sesuai kebutuhan soal UKOM Junior Network Administrator Paket 3.

## 2. Topologi dan Pembagian Interface

Router menggunakan empat interface dengan fungsi sebagai berikut:

| Interface | Nama Interface | Network | Fungsi |
|---|---|---|---|
| Ether1 | Ether1-ISP | Sesuai ISP | Koneksi internet |
| Ether2 | Ether2-CLIENT | 10.10.4.0/28 | Client kabel |
| Ether3 | Ether3-SERVER | 100.100.4.0/30 | Web Server dan Database Server |
| Wlan1 | Wlan1-HOTSPOT | 192.168.4.0/24 | Client wireless |

**Keterangan:** Angka 4 pada oktet ketiga merupakan nomor absen. Apabila nomor absen berbeda, sesuaikan alamat IP berdasarkan nomor masing-masing.

## 3. Konfigurasi Router MikroTik

### 3.1. Menghubungkan Router ke ISP Menggunakan DHCP Client

Konfigurasi DHCP Client dilakukan agar router memperoleh IP Address secara otomatis dari ISP sehingga dapat terhubung ke jaringan internet.

**Langkah-langkah:**

1. Buka aplikasi WinBox dan hubungkan ke router MikroTik.
2. Masuk ke menu **IP → DHCP Client**.
3. Klik tombol **+ (Add)**.
4. Atur konfigurasi berikut:
   - Interface: `ether1`
   - Use Peer DNS: `yes` (opsional)
   - Add Default Route: `yes`
5. Klik **Apply**, kemudian **OK**.

Pastikan DHCP Client memperoleh alamat IP dan statusnya menunjukkan **bound**.

### 3.2. Mengubah Nama Interface

Penggantian nama interface dilakukan untuk mempermudah identifikasi setiap port berdasarkan fungsi jaringan yang digunakan.

**Langkah-langkah:**

1. Buka menu **Interfaces**.
2. Pilih interface yang ingin diubah namanya.
3. Klik dua kali pada interface tersebut.
4. Ubah nama interface sesuai pembagian berikut:

| Interface Awal | Nama Baru |
|---|---|
| ether1 | Ether1-ISP |
| ether2 | Ether2-CLIENT |
| ether3 | Ether3-SERVER |
| wlan1 | Wlan1-HOTSPOT |

5. Klik **Apply**, kemudian **OK** pada setiap interface.

### 3.3. Mengatur IP Address pada Setiap Interface

IP Address diberikan pada masing-masing interface agar router dapat menjadi gateway untuk setiap jaringan lokal.

**Langkah-langkah:**

1. Masuk ke menu **IP → Addresses**.
2. Klik tombol **+ (Add)**.
3. Masukkan IP Address untuk setiap interface.

**Konfigurasi Ether2-CLIENT:**

- Address: `10.10.4.1/28`
- Interface: `Ether2-CLIENT`

**Konfigurasi Ether3-SERVER:**

- Address: `100.100.4.1/30`
- Interface: `Ether3-SERVER`

**Konfigurasi Wlan1-HOTSPOT:**

- Address: `192.168.4.1/24`
- Interface: `Wlan1-HOTSPOT`

4. Klik **Apply**, kemudian **OK**.

Setelah selesai, router memiliki tiga alamat IP gateway untuk jaringan lokal yang berbeda.

### 3.4. Mengatur DNS dan DNS Static

DNS digunakan untuk menerjemahkan nama domain menjadi alamat IP sehingga perangkat dapat mengakses layanan menggunakan nama domain.

**A. Mengaktifkan DNS**

1. Masuk ke menu **IP → DNS**.
2. Pada bagian Servers, masukkan DNS berikut (tapi ga gw masukin, langsung centang allow remote requests):
   - `8.8.8.8`
   - `1.1.1.1`
3. Centang bagian **Allow Remote Requests**.
4. Klik **Apply**, kemudian **OK**.

**B. Menambahkan DNS Static**

DNS Static digunakan untuk mengarahkan nama domain server menuju alamat IP server.

1. Masih di menu **IP → DNS**, buka tab **Static**.
2. Klik tombol **+ (Add)**.
3. Isi konfigurasi berikut:

   - Name: `ukom12tkja-4.sch.id`
   - Type: `A`
   - Address: `100.100.4.2`

4. Klik **Apply**, kemudian **OK**.

Dengan konfigurasi tersebut, domain `ukom12tkja-4.sch.id` diarahkan menuju server dengan IP Address `100.100.4.2`.

### 3.5. Mengatur Firewall NAT

Firewall NAT digunakan agar perangkat pada jaringan lokal dapat mengakses internet melalui IP Address yang diperoleh router dari ISP.

**Langkah-langkah:**

1. Masuk ke menu **IP → Firewall**.
2. Pilih tab **NAT**.
3. Klik tombol **+ (Add)**.
4. Pada tab General, atur:

   - Chain: `srcnat`
   - Out. Interface: `Ether1-ISP`

5. Pindah ke tab Action.
6. Atur:

   - Action: `masquerade`

7. Klik **Apply**, kemudian **OK**.

Konfigurasi ini memungkinkan alamat IP jaringan lokal diterjemahkan menggunakan alamat IP pada interface yang terhubung ke ISP.

### 3.6. Mengatur Wireless pada Wlan1

Wireless dikonfigurasi agar router dapat menyediakan jaringan nirkabel yang nantinya digunakan oleh HP dan laptop melalui layanan Hotspot.

**Langkah-langkah:**

1. Masuk ke menu **WiFi** atau **Wireless**, sesuai versi RouterOS dan perangkat.
2. Pilih interface `wlan1`.
3. Atur konfigurasi berikut:

   - Mode: `ap bridge` (jika tersedia)
   - Band: `2GHz-B/G/N`
   - SSID: `ukom12tkja-4`
   - Frequency: Sesuaikan dengan kondisi jaringan

4. Jika diperlukan, buat Security Profile dengan autentikasi WPA2-PSK dan password wireless.
5. Pastikan interface dalam keadaan aktif atau enabled.
6. Klik **Apply**, kemudian **OK**.

Setelah konfigurasi selesai, SSID akan muncul pada perangkat yang memiliki fitur wireless.

### 3.7. Mengatur Hotspot

Hotspot digunakan untuk mengatur autentikasi pengguna sebelum client dapat mengakses jaringan internet melalui wireless.

**A. Menjalankan Hotspot Setup**

1. Masuk ke menu **IP → Hotspot**.
2. Pilih tab **Servers**.
3. Klik tombol **Hotspot Setup**.
4. Ikuti tahapan konfigurasi berikut:

   - Hotspot Interface: `Wlan1-HOTSPOT`
   - Local Address of Network: `192.168.4.1/24`
   - Address Pool of Network: Gunakan pool yang tersedia atau buat pool khusus, misalnya `192.168.4.2-192.168.4.254`
   - Select Certificate: `none`
   - IP Address of SMTP Server: `0.0.0.0`
   - DNS Servers: `8.8.8.8` (tpi biasanya udah otomatis dapet dri ether1, jdi gausah ditambahin lgi)
   - DNS Name: `ukom12tkja-4.sch.id` (tpi gw gapernah nambahin sih, jdi gausah)
   - Name of Local Hotspot User: Sesuaikan kebutuhan
   - Password: Sesuaikan kebutuhan

5. Klik **Next** sampai proses konfigurasi selesai.

Hotspot Setup akan membuat konfigurasi dasar Hotspot pada interface wireless, termasuk DHCP Server dan IP Pool yang diperlukan.

**B. Membuat User Profile Hotspot**

User Profile digunakan untuk mengatur hak akses dan batas kecepatan setiap pengguna Hotspot.

1. Masuk ke menu **IP → Hotspot**.
2. Pilih tab **User Profiles**.
3. Klik tombol **+ (Add)**.
4. Atur konfigurasi berikut:

   - Name: `PROFILE-UKOM`
   - Address Pool: Pool Hotspot yang telah dibuat
   - Shared Users: `2`
   - Rate Limit: `7M/7M`

5. Klik **Apply**, kemudian **OK**.

Rate Limit `7M/7M` digunakan untuk membatasi kecepatan upload dan download setiap pengguna sesuai pengaturan profil.

**C. Membuat User Hotspot untuk HP**

1. Masuk ke tab **Users**.
2. Klik tombol **+ (Add)**.
3. Masukkan konfigurasi berikut:

   - Server: `all`
   - Name: `hp-arum`
   - Password: `123`
   - Profile: `PROFILE-UKOM`

4. Klik **Apply**, kemudian **OK**.

**D. Membuat User Hotspot untuk Laptop**

1. Masih pada tab **Users**.
2. Klik tombol **+ (Add)**.
3. Masukkan konfigurasi berikut:

   - Server: `all`
   - Name: `laptop-arum`
   - Password: `123`
   - Profile: `PROFILE-UKOM`

4. Klik **Apply**, kemudian **OK**.

Dengan demikian, terdapat dua akun Hotspot yang dapat digunakan untuk login melalui HP dan laptop.

Catatan: Shared Users bernilai 2 berarti satu akun dapat digunakan oleh maksimal dua sesi secara bersamaan. Pengaturan ini bukan batas jumlah akun yang dapat dibuat.

### 3.8. Membuat DHCP Server untuk Ether2-CLIENT

DHCP Server digunakan untuk memberikan IP Address secara otomatis kepada perangkat client yang terhubung melalui kabel LAN pada Ether2.

**A. Menjalankan DHCP Setup**

1. Masuk ke menu **IP → DHCP Server**.
2. Pilih tab **DHCP**.
3. Klik tombol **DHCP Setup**.
4. Ikuti tahapan berikut:

   - DHCP Server Interface: `Ether2-CLIENT`
   - DHCP Address Space: `10.10.4.0/28`
   - Gateway for DHCP Network: `10.10.4.1`
   - Addresses to Give Out: `10.10.4.2-10.10.4.14`
   - DNS Servers: `10.10.4.1` atau DNS yang digunakan
   - Lease Time: Sesuaikan kebutuhan

5. Klik **Next** sampai proses konfigurasi selesai.

**B. Memastikan DHCP Server Aktif**

1. Buka kembali menu **IP → DHCP Server**.
2. Pastikan DHCP Server telah dibuat pada interface `Ether2-CLIENT`.
3. Periksa tab Leases untuk melihat perangkat yang memperoleh IP Address.

Contoh IP Address yang dapat diperoleh client:

`10.10.4.14`

Alamat tersebut merupakan salah satu alamat host yang tersedia pada subnet `10.10.4.0/28`.

### 3.9. Mengatur Queue untuk Client Kabel

Queue digunakan untuk mengatur penggunaan bandwidth agar kecepatan akses internet client kabel tidak melebihi batas yang ditentukan dalam soal UKOM.

Berdasarkan ketentuan UKOM, setiap client kabel memiliki batas kecepatan sebesar **10 Mbps**.

**Langkah-langkah:**

1. Masuk ke menu **Queues**.
2. Pilih tab **Simple Queues**.
3. Klik tombol **+ (Add)**.
4. Pada tab General, atur:

   - Name: `QUEUE-CLIENT-KABEL`
   - Target: `ETHER2-CLIENT`

5. Pada tab General, bagian Max Limit, atur:

   - Max Limit Target Upload: `10M`
   - Max Limit Target Download: `10M`

6. Klik **Apply**, kemudian **OK**.

Dengan konfigurasi tersebut, client kabel memiliki batas maksimum upload dan download sebesar 10 Mbps.

Catatan: Target IP harus disesuaikan dengan IP client yang sebenarnya. Agar aturan Queue tidak berubah ketika DHCP memberikan alamat baru, gunakan Static Lease pada perangkat client.

---

## 4. Pengujian Koneksi Jaringan

Setelah seluruh konfigurasi selesai, lakukan pengujian untuk memastikan setiap jaringan dapat berfungsi sesuai ketentuan UKOM.

### 4.1. Pengujian Koneksi Internet pada Router

1. Buka menu **New Terminal** pada WinBox.
2. Jalankan perintah:

   `ping 8.8.8.8`

3. Perhatikan hasil pengujian.
4. Jika muncul balasan berupa `time=...ms`, berarti router berhasil menjangkau alamat IP tujuan.

Selanjutnya, lakukan pengujian DNS:

`ping google.com`

Jika nama domain berhasil diterjemahkan menjadi alamat IP dan mendapatkan balasan, konfigurasi DNS telah berfungsi.

### 4.2. Pengujian Client Wireless Menggunakan HP

1. Aktifkan WiFi pada HP.
2. Cari SSID `ukom12tkja-4`.
3. Hubungkan HP ke jaringan tersebut menggunakan password wireless apabila telah diaktifkan.
4. Setelah terhubung, buka browser.
5. Tunggu hingga halaman login Hotspot muncul.
6. Masukkan akun berikut:

   - Username: `hp-arum`
   - Password: `123`

7. Klik Login.
8. Pastikan HP berhasil masuk ke jaringan Hotspot.
9. Buka browser dan akses website untuk memastikan koneksi internet berjalan.

### 4.3. Pengujian Client Wireless Menggunakan Laptop

1. Aktifkan WiFi pada laptop.
2. Hubungkan laptop ke SSID `ukom12tkja-4`.
3. Buka browser dan tunggu halaman login Hotspot muncul.
4. Masukkan akun berikut:

   - Username: `laptop-arum`
   - Password: `123`

5. Klik Login.
6. Pastikan laptop berhasil terhubung ke internet.
7. Lakukan pengujian akses website.

### 4.4. Pengujian Client Kabel Menggunakan Laptop

1. Hubungkan laptop ke interface `Ether2-CLIENT` menggunakan kabel LAN.
2. Pastikan pengaturan IPv4 pada laptop menggunakan pilihan Obtain an IP Address Automatically.
3. Buka Command Prompt (CMD).
4. Jalankan perintah:

   `ipconfig`

5. Periksa apakah laptop mendapatkan IP Address secara otomatis dari DHCP Server.

Contoh hasil konfigurasi:

- IPv4 Address: `10.10.4.14`
- Subnet Mask: `255.255.255.240`
- Default Gateway: `10.10.4.1`

6. Lakukan pengujian koneksi gateway:

   `ping 10.10.4.1`

7. Lakukan pengujian koneksi internet:

   `ping 8.8.8.8`

8. Buka browser untuk memastikan laptop dapat mengakses internet.

### 4.5. Pengujian Bandwidth Client

Pengujian bandwidth dilakukan untuk memastikan pembatasan kecepatan pada masing-masing jaringan telah berjalan.

**Client Kabel:**

- Maksimum Download: 10 Mbps
- Maksimum Upload: 10 Mbps

**Client Wireless:**

- Maksimum Download: 7 Mbps
- Maksimum Upload: 7 Mbps

Pengujian dapat dilakukan menggunakan aplikasi Speedtest atau dengan mengamati penggunaan bandwidth pada menu Queues di WinBox.

## 5. Kesimpulan

Berdasarkan konfigurasi dan pengujian yang telah dilakukan, router MikroTik digunakan sebagai gateway internet sekaligus perangkat distribusi jaringan untuk tiga segmen, yaitu jaringan client kabel, jaringan server, dan jaringan wireless Hotspot.

Jaringan client kabel memperoleh IP Address secara otomatis melalui DHCP Server pada Ether2. Sementara itu, jaringan wireless menggunakan layanan Hotspot yang mengharuskan pengguna melakukan autentikasi menggunakan username dan password yang telah dibuat.

Pengaturan Queue digunakan untuk membatasi bandwidth client kabel sebesar 10 Mbps, sedangkan User Profile Hotspot digunakan untuk membatasi bandwidth setiap pengguna wireless sebesar 7 Mbps.

Dengan demikian, konfigurasi router diharapkan dapat memenuhi kebutuhan jaringan sesuai ketentuan UKOM Junior Network Administrator Paket 3.

---

**Catatan Dokumentasi:**

Seluruh hasil konfigurasi dan pengujian dapat dilengkapi dengan screenshot WinBox, CMD, halaman login Hotspot, serta hasil pengujian Speedtest sebagai bukti pelaksanaan praktik.
