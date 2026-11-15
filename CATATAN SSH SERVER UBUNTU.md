# CATATAN SSH SERVER UBUNTU

SSH (Secure Shell) merupakan protokol jaringan yang digunakan untuk mengakses dan mengelola server dari komputer lain secara jarak jauh melalui koneksi yang aman. Pada konfigurasi ini, saya menggunakan Ubuntu Server sebagai SSH Server dengan alamat IP `100.100.3.2` dan Windows sebagai client untuk melakukan pengujian koneksi SSH.

## A. KONFIGURASI DI UBUNTU SERVER

### 1. Memeriksa Alamat IP Ubuntu

Langkah pertama adalah memeriksa alamat IP Ubuntu Server dengan menjalankan perintah:

```bash
ip a
```

Perintah ini digunakan untuk melihat informasi interface jaringan dan memastikan Ubuntu Server menggunakan alamat IP `100.100.3.2`.

### 2. Memperbarui Daftar Paket

Selanjutnya, jalankan perintah berikut:

```bash
sudo apt update
```

Perintah ini digunakan untuk memperbarui daftar paket agar Ubuntu mendapatkan informasi paket terbaru sebelum melakukan instalasi OpenSSH Server.

### 3. Menginstal OpenSSH Server

Jalankan perintah berikut untuk menginstal OpenSSH Server:

```bash
sudo apt install openssh-server -y
```

OpenSSH Server berfungsi untuk menerima koneksi dari komputer client sehingga Ubuntu dapat diakses dan dikelola melalui SSH.

### 4. Membuat SSH Host Key

Setelah instalasi selesai, jalankan perintah berikut:

```bash
sudo ssh-keygen -A
```

Perintah ini digunakan untuk membuat SSH Host Key yang berfungsi sebagai identitas server saat menerima koneksi SSH dari komputer client. Host Key diperlukan agar client dapat mengenali identitas server yang diakses.

### 5. Mengaktifkan Layanan SSH

Jalankan kedua perintah berikut:

```bash
sudo systemctl enable ssh
sudo systemctl restart ssh
```

Perintah `enable` digunakan agar layanan SSH berjalan secara otomatis saat Ubuntu dinyalakan, sedangkan `restart` digunakan untuk menjalankan ulang layanan SSH agar konfigurasi dapat diterapkan.

### 6. Memeriksa Status SSH

Untuk memastikan layanan SSH sudah berjalan, gunakan perintah:

```bash
sudo systemctl status ssh
```

Pastikan hasil pemeriksaan menunjukkan status `active (running)`. Status tersebut menandakan bahwa layanan SSH sudah aktif dan siap menerima koneksi dari komputer client. Tekan tombol `Q` untuk keluar dari tampilan status.

### 7. Memeriksa Port SSH

Selanjutnya, periksa apakah SSH sudah menggunakan port 22 dengan perintah:

```bash
sudo ss -tlnp | grep :22
```

Port 22 merupakan port default yang digunakan oleh SSH. Pastikan hasil pemeriksaan menunjukkan status `LISTEN`, yang berarti server sedang mendengarkan koneksi masuk melalui port tersebut.

### 8. Mengatur Firewall

Jika Ubuntu menggunakan UFW dan firewall dalam keadaan aktif, izinkan koneksi SSH melalui port 22 dengan perintah:

```bash
sudo ufw allow 22/tcp
```

Kemudian periksa aturan firewall menggunakan perintah:

```bash
sudo ufw status
```

Pastikan aturan SSH atau port 22 sudah diizinkan agar komputer client dapat terhubung ke Ubuntu Server.

## B. PENGUJIAN SSH DARI WINDOWS

### 9. Membuka Command Prompt

Setelah konfigurasi di Ubuntu selesai, buka Command Prompt (CMD) pada komputer Windows yang akan digunakan sebagai client. CMD digunakan untuk menjalankan perintah pengujian koneksi ke Ubuntu Server.

### 10. Menguji Koneksi Jaringan

Sebelum melakukan SSH, periksa terlebih dahulu koneksi antara Windows dan Ubuntu dengan menjalankan perintah:

```cmd
ping 100.100.3.2
```

Perintah ini digunakan untuk memastikan komputer Windows dapat menjangkau alamat IP Ubuntu Server. Jika muncul balasan `Reply from 100.100.3.2`, berarti koneksi jaringan antara kedua perangkat berhasil.

### 11. Menghapus Host Key Lama

Jika sebelumnya pernah melakukan koneksi SSH dan muncul pesan `Host key verification failed`, jalankan perintah berikut pada CMD:

```cmd
ssh-keygen -R 100.100.3.2
```

Perintah ini digunakan untuk menghapus catatan Host Key lama yang tersimpan di Windows. Langkah ini diperlukan apabila identitas Host Key Ubuntu berubah sehingga tidak cocok dengan catatan yang tersimpan pada komputer client.

### 12. Menghubungkan Windows ke Ubuntu

Selanjutnya, lakukan koneksi SSH dengan menjalankan perintah:

```cmd
ssh nisa@100.100.3.2
```

Perintah tersebut digunakan untuk menghubungkan komputer Windows ke Ubuntu Server menggunakan username `nisa` dan alamat IP `100.100.3.2`. Sesuaikan username dengan akun yang digunakan pada Ubuntu Server.

### 13. Mengonfirmasi Host Key Baru

Jika baru pertama kali melakukan koneksi atau Host Key berubah, CMD akan menampilkan pertanyaan konfirmasi seperti berikut:

```text
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Ketik `yes`, kemudian tekan Enter untuk menerima dan menyimpan identitas Host Key server yang baru.

### 14. Memasukkan Password Ubuntu

Setelah konfirmasi berhasil, CMD akan meminta password akun Ubuntu. Masukkan password sesuai akun yang digunakan, kemudian tekan Enter. Saat mengetik password, karakter tidak akan terlihat di layar. Hal tersebut merupakan fitur keamanan pada terminal Linux.

### 15. Memastikan Koneksi SSH Berhasil

Jika proses koneksi berhasil, CMD akan menampilkan terminal Ubuntu dengan tampilan seperti berikut:

```text
nisa@ubuntu:~$
```

Tampilan tersebut menandakan bahwa komputer Windows sudah berhasil terhubung dan dapat mengakses Ubuntu Server melalui SSH.

## C. TROUBLESHOOTING SSH

Jika koneksi SSH mengalami kendala, lakukan pemeriksaan sesuai pesan error yang muncul.

1. Jika muncul pesan `Connection refused`, periksa status layanan SSH menggunakan perintah `sudo systemctl status ssh`. Pastikan layanan sudah aktif dan port 22 dalam keadaan `LISTEN`.

2. Jika muncul pesan `Host key verification failed`, jalankan perintah `ssh-keygen -R 10.10.1.2` melalui CMD Windows untuk menghapus catatan Host Key lama, kemudian coba lakukan koneksi kembali.

3. Jika muncul pesan `Permission denied`, periksa kembali username dan password Ubuntu yang digunakan saat melakukan koneksi SSH.

4. Jika muncul pesan `Connection timed out`, periksa alamat IP Ubuntu, koneksi jaringan antara Windows dan Ubuntu, serta aturan firewall yang digunakan.

5. Jika muncul pesan `No hostkeys available`, jalankan perintah `sudo ssh-keygen -A` pada Ubuntu untuk membuat Host Key yang belum tersedia, kemudian restart layanan SSH.

**Catatan:** Jika alamat IP Ubuntu Server berubah, gunakan alamat IP terbaru saat melakukan koneksi dari Windows.

Dengan menyelesaikan seluruh tahapan tersebut, komputer Windows dapat terhubung ke Ubuntu Server melalui SSH sehingga pengelolaan server dapat dilakukan dari jarak jauh menggunakan Command Prompt.
