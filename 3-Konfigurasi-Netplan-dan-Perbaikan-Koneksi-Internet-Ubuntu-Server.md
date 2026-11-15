# Konfigurasi Netplan dan Perbaikan Koneksi Internet Ubuntu Server 24.04.3

## A. Tujuan

Melakukan konfigurasi jaringan pada Ubuntu Server 24.04.3 menggunakan Netplan setelah proses instalasi dan reboot selesai.

Konfigurasi ini bertujuan untuk memastikan Ubuntu Server memiliki alamat IP statis, gateway, dan DNS Server yang benar sehingga dapat berkomunikasi dengan router MikroTik serta mengakses jaringan internet.

Pada tahap instalasi sebelumnya, server sudah diberikan IP Address secara manual. Namun, pengaturan DNS dan routing perlu dipastikan kembali agar koneksi internet dapat berjalan dengan baik.

## B. Informasi Konfigurasi Jaringan

Ubuntu Server terhubung dengan router MikroTik melalui interface Ether3-SERVER.

Pembagian alamat IP yang digunakan adalah:

| Parameter | Nilai |
|---|---|
| Network | 100.100.4.0/30 |
| IP Router MikroTik | 100.100.4.1 |
| IP Ubuntu Server | 100.100.4.2/30 |
| Subnet Mask | 255.255.255.252 |
| Gateway | 100.100.4.1 |
| DNS Primary | 8.8.8.8 |
| DNS Secondary | 1.1.1.1 |
| Network Interface | enp0s3 |

Keterangan:
- `100.100.4.1` merupakan IP Address router MikroTik pada Ether3-SERVER.
- `100.100.4.2` merupakan IP Address statis Ubuntu Server.
- `8.8.8.8` merupakan DNS publik Google.
- `1.1.1.1` merupakan DNS publik Cloudflare.

## C. Langkah-Langkah Konfigurasi Netplan

### 1. Login ke Ubuntu Server

Setelah proses instalasi selesai dan komputer melakukan reboot, Ubuntu Server akan menampilkan halaman login.

Langkah-langkah:

1. Masukkan username yang telah dibuat saat instalasi.
2. Masukkan password pengguna.
3. Tekan Enter untuk masuk ke sistem Ubuntu Server.

Setelah berhasil login, terminal akan menampilkan prompt seperti berikut:

```bash
arum@arum:~$
```

Keterangan:
- `arum` pertama menunjukkan username yang digunakan.
- `arum` kedua menunjukkan hostname komputer.
- `~` menunjukkan direktori home pengguna.
- `$` menunjukkan bahwa pengguna sedang menggunakan hak akses biasa.

### 2. Beralih ke Root User

Sebelum melakukan perubahan konfigurasi jaringan, pengguna perlu memperoleh hak akses administrator agar dapat mengedit file sistem.

Masukkan perintah:

```bash
sudo su
```

Tekan Enter, kemudian masukkan password pengguna apabila diminta.

Apabila berhasil, prompt terminal akan berubah menjadi:

```bash
root@arum:/home/arum#
```

Perubahan tanda `$` menjadi `#` menunjukkan bahwa terminal sekarang menggunakan hak akses root.

**Fungsi:** Perintah `sudo su` digunakan untuk beralih dari pengguna biasa menjadi root sehingga pengguna dapat menjalankan perintah administrasi sistem, termasuk mengubah konfigurasi Netplan.

### 3. Membuka File Konfigurasi Netplan

Setelah mengetahui nama file konfigurasi, buka file tersebut menggunakan editor Nano.

Masukkan perintah:

```bash
nano /etc/netplan/50-cloud-init.yaml
```

Tekan Enter.

Perintah tersebut berfungsi untuk membuka file konfigurasi Netplan agar alamat IP, DNS Server, dan gateway dapat diatur secara manual.

### 4. Mengatur Konfigurasi IP Address, DNS, dan Gateway

Setelah file terbuka, sesuaikan seluruh konfigurasi menjadi seperti berikut:

```yaml
network:
  version: 2
  ethernets:
    enp0s3:
      addresses:
        - "100.100.4.2/30"
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1
      routes:
        - to: default
          via: "100.100.4.1"
```

**Penjelasan setiap konfigurasi:**

**a. network**

```yaml
network:
```

Menunjukkan bahwa bagian tersebut merupakan konfigurasi utama jaringan pada Ubuntu Server.

**b. version: 2**

```yaml
version: 2
```

Menentukan versi format konfigurasi Netplan yang digunakan.

**c. ethernets**

```yaml
ethernets:
```

Menunjukkan bahwa konfigurasi yang dibuat ditujukan untuk interface jaringan Ethernet atau jaringan kabel.

**d. enp0s3**

```yaml
enp0s3:
```

Menunjukkan nama interface jaringan yang digunakan oleh Ubuntu Server untuk terhubung ke router MikroTik melalui kabel LAN.

**e. addresses**

```yaml
addresses:
  - "100.100.4.2/30"
```

Digunakan untuk menentukan alamat IP statis yang akan diberikan kepada interface enp0s3.

Ubuntu Server menggunakan IP `100.100.4.2/30` agar berada dalam satu jaringan dengan interface Ether3-SERVER pada MikroTik.

**f. nameservers**

```yaml
nameservers:
  addresses:
    - 8.8.8.8
    - 1.1.1.1
```

Digunakan untuk menentukan DNS Server yang bertugas menerjemahkan nama domain menjadi alamat IP.

DNS yang digunakan adalah:
- `8.8.8.8`: DNS Google.
- `1.1.1.1`: DNS Cloudflare.

Dengan konfigurasi tersebut, Ubuntu Server dapat melakukan resolusi nama domain, misalnya ketika mengakses `google.com`.

**g. routes**

```yaml
routes:
  - to: default
    via: "100.100.4.1"
```

Digunakan untuk menentukan jalur routing default yang akan digunakan ketika Ubuntu Server mengakses jaringan di luar subnet lokal.

Alamat `100.100.4.1` merupakan gateway yang mengarah ke router MikroTik.

Dengan demikian, seluruh komunikasi menuju jaringan lain akan diteruskan melalui router tersebut.

### 5. Menyimpan Konfigurasi Netplan

Setelah seluruh konfigurasi selesai dimasukkan ke dalam Nano, lakukan penyimpanan.

Langkah-langkah:

1. Tekan `Ctrl + O` untuk menyimpan perubahan.
2. Tekan Enter untuk mengonfirmasi nama file.
3. Tekan `Ctrl + X` untuk keluar dari editor Nano.

Setelah itu, terminal akan kembali menampilkan prompt root.

### 6. Menerapkan Konfigurasi Netplan

Setelah file konfigurasi disimpan, terapkan perubahan menggunakan perintah:

```bash
netplan generate
```

**Fungsi:** Memeriksa dan menghasilkan konfigurasi jaringan berdasarkan file YAML Netplan. Perintah ini membantu mendeteksi kesalahan penulisan sintaks maupun struktur konfigurasi sebelum diterapkan.

Apabila tidak muncul pesan kesalahan, lanjutkan dengan:

```bash
netplan try
```

**Fungsi:** Menguji penerapan konfigurasi jaringan dan memberikan kesempatan untuk mengonfirmasi perubahan. Jika tidak dikonfirmasi, konfigurasi dapat dikembalikan ke kondisi sebelumnya setelah waktu konfirmasi berakhir.

Apabila konfigurasi sudah benar, konfirmasikan perubahan sesuai instruksi yang muncul.

Selanjutnya, terapkan konfigurasi menggunakan:

```bash
netplan apply
```

**Fungsi:** Menerapkan konfigurasi jaringan Netplan ke interface yang digunakan sehingga IP Address, DNS, dan routing dapat bekerja sesuai pengaturan terbaru.

## D. Pengujian Koneksi Jaringan

Setelah konfigurasi Netplan berhasil diterapkan, lakukan pengujian untuk memastikan komunikasi jaringan dan akses internet sudah berjalan.

### 1. Memeriksa IP Address

Masukkan perintah:

```bash
ip addr
```

Perintah ini digunakan untuk menampilkan alamat IP yang terpasang pada setiap interface jaringan.

Pastikan interface `enp0s3` memiliki alamat:

```text
100.100.4.2/30
```

Apabila alamat tersebut muncul, berarti IP Address statis sudah diterapkan.

### 2. Memeriksa Default Gateway

Masukkan perintah:

```bash
ip route
```

Perintah ini digunakan untuk menampilkan tabel routing yang dimiliki Ubuntu Server.

Pastikan terdapat informasi default route seperti berikut:

```text
default via 100.100.4.1 dev enp0s3
```

Informasi tersebut menunjukkan bahwa koneksi menuju jaringan luar akan diteruskan melalui gateway MikroTik.

### 3. Menguji Koneksi ke Router MikroTik

Sebelum menguji koneksi internet, pastikan Ubuntu Server dapat berkomunikasi dengan router terlebih dahulu.

Masukkan perintah:

```bash
ping 100.100.4.1
```

Perintah tersebut digunakan untuk menguji konektivitas antara Ubuntu Server dan router MikroTik.

Apabila muncul balasan seperti:

```text
64 bytes from 100.100.4.1
```

Berarti komunikasi antara server dan router berhasil.

Tekan `Ctrl + C` untuk menghentikan pengujian.

### 4. Menguji Koneksi Internet Menggunakan IP Address

Setelah koneksi ke gateway berhasil, lanjutkan dengan menguji koneksi internet menggunakan alamat IP publik.

Masukkan perintah:

```bash
ping 8.8.8.8
```

Perintah tersebut digunakan untuk menguji apakah Ubuntu Server dapat menjangkau alamat IP publik Google melalui jaringan internet.

Apabila muncul balasan seperti:

```text
64 bytes from 8.8.8.8
```

Berarti server telah berhasil menjangkau alamat IP tersebut.

### 5. Menguji Resolusi DNS Menggunakan Domain

Setelah pengujian IP Address berhasil, lanjutkan dengan menguji resolusi DNS.

Masukkan perintah:

```bash
ping google.com
```

Perintah tersebut digunakan untuk memastikan Ubuntu Server dapat menerjemahkan nama domain `google.com` menjadi alamat IP melalui DNS Server yang telah dikonfigurasi.

Apabila terminal menampilkan informasi seperti:

```text
PING google.com (142.251.xxx.xxx)
```

Kemudian diikuti balasan ping, berarti DNS berhasil melakukan proses penerjemahan nama domain menjadi alamat IP.

Tekan `Ctrl + C` untuk menghentikan pengujian.

## E. Hasil Akhir yang Diharapkan

Setelah seluruh konfigurasi selesai, Ubuntu Server diharapkan memiliki kondisi berikut:

| No. | Pengujian | Hasil yang Diharapkan |
|---|---|---|
| 1 | IP Address | 100.100.4.2/30 |
| 2 | Gateway | 100.100.4.1 |
| 3 | DNS Primary | 8.8.8.8 |
| 4 | DNS Secondary | 1.1.1.1 |
| 5 | Ping Gateway | Berhasil |
| 6 | Ping 8.8.8.8 | Berhasil |
| 7 | Ping google.com | Berhasil |
