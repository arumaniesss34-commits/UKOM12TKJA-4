# Konfigurasi MikroTik dan Pengujian Jaringan UKOM

**Nama:** Arum Wigati  
**Nomor Absen:** 04  
**Materi:** Konfigurasi MikroTik, Hotspot, DHCP Server, Simple Queue, dan Ubuntu Server

## 1. Persiapan Router MikroTik

1. Atur IP pada Control Panel laptop yang digunakan untuk mengakses Winbox, yaitu `10.10.25.2/28`.
2. Lakukan **Reset Configuration** pada router MikroTik jika diperlukan.
3. Buka Winbox dan hubungkan ke router.
4. Atur nama interface sesuai fungsinya:

   | Interface | Fungsi |
   |---|---|
   | `ether1` | ISP/WAN |
   | `ether2` | Jaringan server |
   | `ether3` | Jaringan client kabel |
   | `wlan1` | Jaringan Hotspot |

## 2. Konfigurasi IP Address

Masuk ke **IP → Addresses**, lalu tambahkan alamat IP berikut.

| Interface | IP Address |
|---|---|
| `ether2` | `10.10.4.1/28` |
| `ether3` | `100.100.4.1/30` |
| `wlan1` | `192.168.4.1/24` |

Selanjutnya, masuk ke **IP → DHCP Client**, tambahkan DHCP Client pada `ether1` agar router memperoleh IP dari ISP.

## 3. Konfigurasi DHCP Server pada ether2

1. Buka **IP → Pool**, lalu buat pool IP untuk perangkat yang menggunakan jaringan `ether2`. Pastikan IP statis yang sudah digunakan tidak dimasukkan ke dalam pool.
2. Buka **IP → DHCP Server → DHCP Setup**.
3. Pilih interface `ether2`.
4. Gunakan network `10.10.4.0/28` dan gateway `10.10.4.1`.
5. Pilih pool yang sudah dibuat, lalu selesaikan DHCP Setup.
6. Pastikan DHCP Server berstatus aktif.

> **Catatan:** Jika Ubuntu Server menggunakan IP statis `10.10.4.3/28` dan laptop pengelola menggunakan `10.10.4.2/28`, jangan masukkan kedua alamat tersebut ke dalam DHCP pool.

## 4. Konfigurasi NAT

1. Masuk ke **IP → Firewall → NAT**.
2. Klik **+** untuk menambahkan rule.
3. Pada tab **General**, atur:
   - **Chain:** `srcnat`
   - **Out. Interface:** `ether1`
4. Pada tab **Action**, pilih `masquerade`.
5. Klik **Apply**, lalu **OK**.

Konfigurasi ini memungkinkan perangkat dalam jaringan lokal mengakses internet melalui router MikroTik.

## 5. Konfigurasi DNS MikroTik

1. Buka **IP → DNS**.
2. Isi **Servers** dengan `1.1.1.1` dan `8.8.8.8`.
3. Centang **Allow Remote Requests** jika diperlukan.
4. Klik **Apply**, lalu **OK**.

Setelah Ubuntu Server selesai dikonfigurasi dan layanan DNS aktif, tambahkan DNS Static untuk domain website:

1. Masuk ke **IP → DNS → Static**.
2. Klik **+**.
3. Isi:
   - **Name:** `ukom12tkj-4.sch.id`
   - **Type:** `A`
   - **Address:** `10.10.4.3`
4. Klik **Apply**, lalu **OK**.

Alamat `10.10.4.3` adalah IP Ubuntu Server yang digunakan pada konfigurasi ini. Sesuaikan jika IP server yang ditetapkan penguji berbeda.

## 6. Konfigurasi WiFi

1. Buka menu **WiFi** atau **Wireless**, sesuai versi RouterOS.
2. Pilih interface `wlan1`.
3. Atur mode menjadi `ap bridge` jika tersedia.
4. Pilih band yang sesuai dengan perangkat.
5. Isi SSID sesuai ketentuan UKOM, misalnya `ukom12tkj-4`.
6. Aktifkan interface, lalu klik **Apply** dan **OK**.

## 7. Konfigurasi Hotspot

1. Buka **IP → Hotspot**.
2. Klik **Hotspot Setup**, lalu pilih interface `wlan1`.
3. Gunakan gateway `192.168.4.1/24`.
4. Atur IP Pool, misalnya `192.168.4.10-192.168.4.254`.
5. Setelah selesai, buka tab **User Profiles**.
6. Buat atau sesuaikan profil dengan pengaturan:
   - **Address Pool:** pool Hotspot yang dibuat.
   - **Shared Users:** `2`.
   - **Rate Limit:** `7M/7M`.
7. Buka tab **Users**, lalu buat akun Hotspot sesuai ketentuan pengujian.

## 8. Pengujian Awal Speed Test

Lakukan pengujian jaringan **sebelum melanjutkan instalasi dan konfigurasi Ubuntu Server**.

### A. Pengujian Hotspot

1. Hubungkan HP atau laptop ke WiFi Hotspot.
2. Masukkan username dan password pada halaman login Hotspot.
3. Setelah berhasil login, buka situs Speedtest.
4. Jalankan pengujian kecepatan internet.
5. Pastikan kecepatan Hotspot tidak melebihi **7 Mbps**, sesuai batas pada User Profile.

### B. Pengujian Client Kabel

1. Hubungkan laptop ke interface `ether3`.
2. Atur IPv4 laptop secara manual:
   - **IP Address:** `100.100.4.2`
   - **Subnet Mask:** `255.255.255.252`
   - **Default Gateway:** `100.100.4.1`
   - **DNS Server:** `8.8.8.8`
3. Buka situs Speedtest dan jalankan pengujian.
4. Pastikan kecepatan jaringan kabel maksimal **10 Mbps**.

Jika hasil pengujian melebihi batas, periksa kembali pengaturan Hotspot User Profile dan Simple Queue.

## 9. Konfigurasi Simple Queue untuk Client Kabel

1. Buka **Queues → Simple Queues**.
2. Klik **+**.
3. Isi pengaturan berikut:
   - **Name:** `Client-Kabel`
   - **Target:** `100.100.4.2/32`
   - **Max Limit:** `10M/10M`
4. Klik **Apply**, lalu **OK**.
5. Jalankan kembali Speedtest pada laptop kabel untuk memastikan kecepatannya tidak melebihi batas 10 Mbps.

## 10. Instalasi dan Konfigurasi Ubuntu Server

Setelah konfigurasi MikroTik selesai dan pengujian awal berhasil, lanjutkan instalasi Ubuntu Server.

1. Instal Ubuntu Server pada perangkat atau mesin virtual yang digunakan.
2. Konfigurasikan IP Address server sesuai rancangan jaringan `10.10.4.0/28`.
3. Atur IP Ubuntu Server menjadi `10.10.4.3/28`.
4. Atur gateway menjadi `10.10.4.1`.
5. Pastikan server dapat berkomunikasi dengan router MikroTik.
6. Lakukan pengujian koneksi dan pastikan IP yang digunakan sesuai konfigurasi.

## 11. Pengujian DNS Website

Setelah Ubuntu Server terpasang dan layanan DNS sudah dikonfigurasi:

1. Pastikan layanan DNS berjalan pada Ubuntu Server.
2. Pastikan domain `ukom12tkj-4.sch.id` mengarah ke IP Ubuntu Server `10.10.4.3`.
3. Dari laptop client, lakukan pengujian DNS menggunakan `nslookup`.
4. Pastikan hasil resolusi domain menunjukkan IP server yang benar.
5. Buka domain website melalui browser dan pastikan website dapat diakses.

## 12. Kesimpulan

Konfigurasi ini mencakup pengaturan interface dan IP Address MikroTik, DHCP Server, NAT, DNS, WiFi, Hotspot, serta pembatasan kecepatan melalui User Profile dan Simple Queue. Pengujian dilakukan terlebih dahulu pada jaringan Hotspot dan kabel. Setelah jaringan berjalan sesuai batas kecepatan, instalasi Ubuntu Server dan pengujian domain website dilanjutkan.

## Catatan Sebelum Digunakan

- Dokumen ini menggunakan jaringan `10.10.4.0/28` dan IP server `10.10.4.3/28`. Jika kisi-kisi penguji menetapkan jaringan berbeda, sesuaikan seluruh IP terkait.
- Pastikan nama domain dan SSID sama persis dengan ketentuan penguji.
- DNS Static di MikroTik membantu klien yang menggunakan MikroTik sebagai DNS resolver. Untuk domain lokal, konfigurasi DNS pada Ubuntu Server dan DNS yang dipakai klien juga harus sesuai.
