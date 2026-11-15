# Instalasi dan Konfigurasi Ubuntu Server 24.04.3 pada Jaringan Ether3-SERVER

## A. Tujuan

Melakukan instalasi sistem operasi Ubuntu Server 24.04.3 LTS pada komputer yang difungsikan sebagai server, kemudian menghubungkannya dengan router MikroTik melalui interface Ether3-SERVER.

Konfigurasi jaringan dilakukan secara manual (Static IP) karena DHCP Server pada router hanya diaktifkan untuk jaringan client kabel melalui Ether2-CLIENT.

Pada tahap ini, server tetap terhubung secara fisik dengan router MikroTik, tetapi instalasi dilakukan tanpa koneksi internet.

## B. Persiapan

Peralatan dan bahan yang diperlukan:

1. Satu komputer khusus untuk server.
2. File ISO `ubuntu-24.04.3-live-server-amd64`.
3. Satu buah router MikroTik yang telah dikonfigurasi.
4. Kabel UTP/LAN untuk menghubungkan komputer server dengan router.
5. Monitor, keyboard, dan perangkat pendukung lainnya.

### Pembagian IP Address

Sebelum memulai instalasi, pastikan interface Ether3-SERVER pada MikroTik telah memiliki konfigurasi IP Address berikut:

| Perangkat | Interface | IP Address |
|---|---|---|
| MikroTik | Ether3-SERVER | 100.100.4.1/30 |
| Ubuntu Server | enp0s3 | 100.100.4.2/30 |

Keterangan:
- Network: `100.100.4.0/30`
- Subnet Mask: `255.255.255.252`
- Gateway: `100.100.4.1`

## C. Langkah-Langkah Instalasi Ubuntu Server

### 1. Menghubungkan Komputer Server ke Router MikroTik

1. Siapkan komputer yang akan digunakan sebagai server.
2. Hubungkan salah satu ujung kabel LAN ke port Ethernet komputer server.
3. Hubungkan ujung kabel lainnya ke interface Ether3-SERVER pada router MikroTik.
4. Pastikan perangkat telah terhubung secara fisik.
5. Siapkan media instalasi Ubuntu Server 24.04.3 menggunakan flashdisk bootable atau media ISO pada mesin virtual.
6. Nyalakan komputer dan jalankan proses booting melalui media instalasi Ubuntu Server.

### 2. Memulai Instalasi Ubuntu Server

1. Setelah komputer berhasil melakukan booting melalui media instalasi, tunggu hingga tampilan awal Ubuntu Server muncul.
2. Pilih opsi instalasi Ubuntu Server.
3. Tekan Enter untuk melanjutkan.
4. Ikuti pengaturan awal seperti pemilihan bahasa, keyboard layout, dan jenis instalasi sesuai kebutuhan.
5. Lanjutkan hingga masuk ke halaman **Network Configuration**.

### 3. Mengatur Network Configuration

Pada halaman Network Configuration, installer akan mendeteksi interface jaringan yang tersedia pada komputer server.

Berdasarkan perangkat yang digunakan, interface jaringan terdeteksi dengan nama `enp0s3`.

Pada bagian ini, terdapat keterangan `autoconfiguration failed`. Kondisi tersebut terjadi karena Ubuntu Server mencoba memperoleh alamat IP secara otomatis melalui DHCP, sedangkan pada Ether3-SERVER MikroTik tidak terdapat DHCP Server.

Oleh karena itu, konfigurasi IP Address dilakukan secara manual.

Langkah-langkahnya:

1. Pada halaman Network Configuration, pilih interface `enp0s3`.
2. Tekan Enter untuk membuka pengaturan interface.
3. Pilih menu **Edit IPv4**.

### 4. Memilih Metode Konfigurasi IPv4 Manual

Setelah memilih Edit IPv4, akan muncul beberapa pilihan metode konfigurasi:

- Automatic (DHCP)
- Manual
- Disabled

Karena server menggunakan IP Address statis, pilih opsi **Manual**.

Langkah-langkah:

1. Arahkan pilihan ke `Manual`.
2. Tekan Enter.
3. Setelah itu, akan muncul halaman Edit enp0s3 IPv4 configuration.
4. Masukkan alamat jaringan dan IP Address sesuai pembagian subnet server.

### 5. Mengisi Konfigurasi IP Address Ubuntu Server

Pada halaman Edit enp0s3 IPv4 configuration, terdapat beberapa kolom yang harus diisi.

Masukkan konfigurasi berikut:

| Parameter | Nilai |
|---|---|
| IPv4 Method | Manual |
| Subnet | 100.100.4.0/30 |
| Address | 100.100.4.2 |
| Gateway | 100.100.4.1 |
| Name servers | Kosongkan |
| Search domains | Kosongkan |

**Penjelasan setiap parameter:**

- **IPv4 Method:** Memilih metode konfigurasi IP secara manual agar alamat IP tidak berubah secara otomatis.
- **Subnet:** Menentukan jaringan yang digunakan oleh server, yaitu `100.100.4.0/30`.
- **Address:** Menentukan IP Address khusus untuk Ubuntu Server, yaitu `100.100.4.2`.
- **Gateway:** Menentukan alamat IP router MikroTik yang menjadi penghubung jaringan server, yaitu `100.100.4.1`.
- **Name Servers:** Dapat dikosongkan karena instalasi dilakukan tanpa koneksi internet dan belum memerlukan DNS eksternal.
- **Search Domains:** Dapat dikosongkan karena belum ada domain pencarian tambahan yang diperlukan.

Setelah seluruh konfigurasi diisi:

1. Arahkan pilihan ke **Save**.
2. Tekan Enter untuk menyimpan konfigurasi.

### 6. Menyelesaikan Network Configuration

Setelah konfigurasi disimpan, installer akan kembali ke halaman Network Configuration.

Pada bagian interface `enp0s3`, seharusnya sudah terlihat informasi:

- Interface: `enp0s3`
- Status konfigurasi: `static`
- IP Address: `100.100.4.2/30`

Hal tersebut menunjukkan bahwa alamat IP berhasil diterapkan secara manual.

Langkah selanjutnya:

1. Pastikan konfigurasi IP Address sudah sesuai.
2. Pilih **Done** pada bagian bawah halaman.
3. Tekan Enter untuk melanjutkan.
4. Apabila installer menampilkan pilihan **Continue without network**, pilih opsi tersebut karena instalasi memang dilakukan tanpa koneksi internet.
5. Lanjutkan proses instalasi Ubuntu Server.

**Catatan:** Continue without network digunakan untuk melanjutkan instalasi tanpa akses internet. Hal tersebut tidak berarti koneksi fisik Ethernet ke router harus dilepas.

### 7. Melanjutkan Instalasi Ubuntu Server

Setelah konfigurasi jaringan selesai, lanjutkan tahapan instalasi hingga selesai.

Beberapa pengaturan yang perlu dilakukan:

1. Pilih jenis instalasi Ubuntu Server sesuai kebutuhan.
2. Atur penyimpanan atau disk partitioning.
3. Buat profil pengguna server, meliputi nama pengguna, nama komputer, username, dan password.
4. Atur zona waktu apabila diminta.
5. Lanjutkan proses instalasi hingga selesai.
6. Tunggu sampai installer menyelesaikan proses pemasangan sistem operasi.
7. Pilih **Reboot Now** apabila muncul instruksi untuk memulai ulang komputer.
8. Lepaskan media instalasi jika diminta.
9. Tunggu hingga Ubuntu Server berhasil masuk ke halaman login.

## D. Verifikasi Konfigurasi Jaringan Ubuntu Server

Setelah instalasi selesai, lakukan pemeriksaan untuk memastikan IP Address telah diterapkan dengan benar dan komunikasi antara server dengan router MikroTik dapat berjalan.

### 1. Memeriksa IP Address

1. Login ke Ubuntu Server menggunakan username dan password yang telah dibuat.
2. Masukkan perintah berikut:

   ```bash
   ip addr
   ```

3. Tekan Enter.
4. Cari interface `enp0s3`.
5. Pastikan terdapat alamat IP:

   ```text
   100.100.4.2/30
   ```

Apabila alamat tersebut muncul pada interface enp0s3, konfigurasi IP Address berhasil diterapkan.

### 2. Memeriksa Default Gateway

Untuk melihat konfigurasi routing pada Ubuntu Server, masukkan perintah:

```bash
ip route
```

Pastikan terdapat informasi default gateway yang mengarah ke:

```text
default via 100.100.4.1 dev enp0s3
```

Selain itu, pastikan jaringan `100.100.4.0/30` terhubung langsung melalui interface `enp0s3`.

### 3. Menguji Koneksi ke Router MikroTik

Pengujian dilakukan untuk memastikan Ubuntu Server dapat berkomunikasi dengan router melalui Ether3-SERVER.

Masukkan perintah:

```bash
ping 100.100.4.1
```

Tekan Enter.

Apabila muncul balasan berupa `bytes from 100.100.4.1`, berarti Ubuntu Server berhasil berkomunikasi dengan router MikroTik.

Tekan `Ctrl + C` untuk menghentikan proses ping.

## E. Hasil Akhir yang Diharapkan

Setelah seluruh tahapan instalasi dan konfigurasi selesai, hasil yang diharapkan adalah:

| No. | Parameter | Hasil |
|---|---|---|
| 1 | Sistem Operasi | Ubuntu Server 24.04.3 LTS |
| 2 | Network Interface | enp0s3 |
| 3 | IPv4 Method | Manual |
| 4 | IP Address | 100.100.4.2 |
| 5 | Subnet Prefix | /30 |
| 6 | Gateway | 100.100.4.1 |
| 7 | Koneksi ke Router | Berhasil |
| 8 | Koneksi Internet | Tidak diperlukan pada tahap instalasi |

## F. Kesimpulan

Instalasi Ubuntu Server 24.04.3 dilakukan dengan menghubungkan komputer server ke router MikroTik melalui interface Ether3-SERVER. Karena jaringan server tidak menggunakan DHCP Server, konfigurasi IP Address dilakukan secara manual dengan metode Static.

Ubuntu Server menggunakan IP Address `100.100.4.2/30`, sedangkan router MikroTik menggunakan IP Address `100.100.4.1/30` sebagai gateway. Meskipun instalasi dilakukan tanpa koneksi internet, server tetap terhubung secara lokal dengan router MikroTik.

Pengujian menggunakan perintah `ip addr`, `ip route`, dan `ping` dilakukan untuk memastikan konfigurasi jaringan telah diterapkan dengan benar serta komunikasi antara Ubuntu Server dan router MikroTik dapat berjalan.
