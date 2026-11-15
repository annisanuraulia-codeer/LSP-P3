# A. INSTALASI DAN KONFIGURASI DATABASE MARIADB

Sebelum melakukan instalasi MariaDB, saya terlebih dahulu mengatur IP Address pada Ubuntu Server. Langkah ini bertujuan agar Ubuntu Server dapat terhubung dengan jaringan Server pada MikroTik melalui interface ether3.

## 1. Konfigurasi Awal IP Address Ubuntu Server

### 1.1 Memeriksa Nama Interface

Nyalakan Ubuntu Server, kemudian buka terminal dan jalankan perintah:

```bash
ip -br a
```

Digunakan untuk melihat nama interface jaringan dan statusnya. Nama interface yang muncul akan digunakan pada konfigurasi berikutnya.

### 1.2 Memeriksa File Netplan

```bash
ls /etc/netplan/
```

Digunakan untuk melihat file konfigurasi jaringan yang tersedia pada Ubuntu Server.

Jika muncul file `50-cloud-init.yaml`, buka menggunakan perintah:

```bash
sudo nano /etc/netplan/50-cloud-init.yaml
```

Perintah ini digunakan untuk membuka file Netplan agar konfigurasi IP Address dapat diatur. Sesuaikan nama file dengan hasil pemeriksaan sebelumnya.

### 1.3 Mengatur IP Address Ubuntu Server

Sesuaikan konfigurasi jaringan dengan pengaturan berikut. Jika nama interface berbeda, ganti `enp0s3` sesuai nama interface milikmu.

```yaml
network:
  ethernets:
    enp0s3:
      addresses:
        - 100.100.3.2/30
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1
        search: []
      routes:
        - to: default
          via: 100.100.3.1
  version: 2
```

Konfigurasi ini digunakan untuk memberikan IP Address statis pada Ubuntu Server, menentukan DNS, dan mengatur gateway menuju MikroTik.

- `100.100.3.2/30`: IP Address Ubuntu Server.
- `8.8.8.8` dan `1.1.1.1`: DNS Server.
- `100.100.3.1`: Gateway menuju MikroTik.
- `version: 2`: Versi konfigurasi Netplan.

Simpan konfigurasi dengan menekan `Ctrl + O`, lalu `Enter`. Setelah itu, tekan `Ctrl + X` untuk keluar.

### 1.4 Menerapkan Konfigurasi Netplan

```bash
sudo netplan try
```

Digunakan untuk menguji konfigurasi jaringan sebelum diterapkan. Jika berhasil, konfirmasi sesuai petunjuk yang muncul.

```bash
sudo netplan apply
```

Digunakan untuk menerapkan konfigurasi jaringan yang sudah dibuat.

### 1.5 Menguji Koneksi Ubuntu Server

```bash
ip a
```

Digunakan untuk memeriksa IP Address yang sudah terpasang pada interface Ubuntu Server. Pastikan IP yang muncul adalah `100.100.3.2/30`.

```bash
ping 100.100.3.1
```

Digunakan untuk menguji koneksi Ubuntu Server ke gateway MikroTik.

```bash
ping google.com
```

Digunakan untuk menguji koneksi internet sekaligus memastikan DNS dapat menerjemahkan nama domain.

Jika pengujian berhasil, Ubuntu Server sudah dapat berkomunikasi dengan gateway dan melakukan penerjemahan nama domain.

## 2. Memperbarui Daftar Paket Ubuntu

```bash
sudo apt update
```

Digunakan untuk mengambil informasi paket terbaru dari repository Ubuntu. Tujuannya agar sistem mengetahui versi paket yang tersedia sebelum melakukan instalasi.

## 3. Menginstal MariaDB Server dan Client

```bash
sudo apt install mariadb-server mariadb-client -y
```

Digunakan untuk menginstal MariaDB pada Ubuntu Server.

- `mariadb-server`: Menjalankan layanan database.
- `mariadb-client`: Mengakses dan mengelola database melalui terminal.
- `-y`: Menyetujui proses instalasi secara otomatis.

MariaDB digunakan untuk menyimpan, mengatur, dan mengambil data dalam database.

## 4. Mengaktifkan dan Memeriksa Layanan MariaDB

```bash
sudo systemctl enable --now mariadb
```

Digunakan untuk mengaktifkan layanan MariaDB secara otomatis saat server dinyalakan sekaligus langsung menjalankannya.

Untuk memeriksa status layanan, jalankan:

```bash
sudo systemctl status mariadb
```

Pastikan muncul status `active (running)`, yang menandakan layanan MariaDB berhasil berjalan. Tekan `q` untuk keluar dari tampilan status.

## 5. Melakukan Pengamanan Awal MariaDB

```bash
sudo mariadb-secure-installation
```

Digunakan untuk melakukan pengamanan awal MariaDB, seperti mengatur autentikasi administrator, menghapus akun anonim, dan mengatur akses root.

Ikuti pertanyaan yang muncul sesuai kebutuhan konfigurasi. Setelah selesai, masuk ke konsol MariaDB menggunakan:

```bash
sudo mariadb
```

Perintah ini digunakan untuk masuk ke konsol MariaDB melalui akun administrator lokal sehingga database dapat dibuat dan dikelola.

## 6. Membuat Database

Setelah masuk ke konsol MariaDB, buat database dengan nama `ukom3`:

```sql
CREATE DATABASE ukom3
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_ci;
```

Digunakan untuk membuat database baru bernama `ukom3`. `utf8mb4` mendukung berbagai karakter teks, sedangkan `COLLATE` mengatur perbandingan dan pengurutan teks.

Untuk memastikan database berhasil dibuat:

```sql
SHOW DATABASES;
```

Digunakan untuk menampilkan daftar database yang tersedia. Pastikan `ukom3` sudah muncul.

## 7. Membuat Dua User Database

### A. Membuat User Pertama

```sql
CREATE USER 'ukomtkj-1'@'localhost'
IDENTIFIED BY 'UkomDB@2026';
```

Digunakan untuk membuat akun `ukomtkj-1` dengan password yang sudah ditentukan. `localhost` berarti akun hanya dapat digunakan dari server lokal.

### B. Membuat User Kedua

```sql
CREATE USER 'ukomtkj-2'@'localhost'
IDENTIFIED BY 'UkomDB@2026';
```

Digunakan untuk membuat akun kedua, yaitu `ukomtkj-2`, dengan password yang sama.

## 8. Memberikan Hak Akses kepada Kedua User

```sql
GRANT ALL PRIVILEGES ON ukom3.* TO 'ukomtkj-1'@'localhost';

GRANT ALL PRIVILEGES ON ukom3.* TO 'ukomtkj-2'@'localhost';
```

Digunakan untuk memberikan seluruh hak akses kepada kedua user pada database `ukom3`, termasuk mengelola tabel dan data di dalamnya.

Kemudian jalankan:

```sql
FLUSH PRIVILEGES;
```

Digunakan untuk memuat ulang informasi hak akses agar perubahan izin pengguna diterapkan.

## 9. Membuat Tabel Informasi

Pilih database yang akan digunakan:

```sql
USE ukom3;
```

Digunakan untuk memilih database `ukom3` sebagai tempat penyimpanan tabel.

Selanjutnya, buat tabel bernama `informasi`:

```sql
CREATE TABLE informasi (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nama VARCHAR(100) NOT NULL,
    keterangan TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

Digunakan untuk membuat tabel `informasi` dengan empat kolom:

- `id`: Nomor identitas unik yang bertambah otomatis.
- `nama`: Menyimpan nama data dengan panjang maksimal 100 karakter.
- `keterangan`: Menyimpan informasi tambahan berupa teks.
- `created_at`: Mencatat waktu pembuatan data secara otomatis.

Untuk memastikan tabel berhasil dibuat:

```sql
SHOW TABLES;
```

Digunakan untuk menampilkan daftar tabel pada database aktif. Pastikan tabel `informasi` sudah muncul.

## 10. Memasukkan Data ke Dalam Tabel

```sql
INSERT INTO informasi (nama, keterangan)
VALUES
('UKOM TKJ', 'Web Server dan Database Server'),
('Absen 3', 'Konfigurasi jaringan UKOM'),
('MikroTik', 'Gateway dan HotSpot'),
('Server', 'Ubuntu Server');
```

Digunakan untuk memasukkan empat data contoh ke dalam tabel `informasi`. Kolom `id` dan `created_at` akan terisi secara otomatis sesuai pengaturan tabel.

Untuk melihat data yang sudah dimasukkan:

```sql
SELECT * FROM informasi;
```

Digunakan untuk menampilkan seluruh kolom dan data yang tersimpan pada tabel `informasi`.

Setelah selesai, keluar dari konsol MariaDB:

```sql
EXIT;
```

Digunakan untuk keluar dari konsol MariaDB dan kembali ke terminal Ubuntu.

## 11. Pengujian Login User Pertama

Pada terminal Ubuntu, jalankan:

```bash
mariadb -u ukomtkj-1 -p ukom3
```

Digunakan untuk login ke database `ukom3` menggunakan akun pertama, yaitu `ukomtkj-1`. Masukkan password ketika diminta.

Setelah berhasil masuk, jalankan:

```sql
SELECT * FROM informasi;
```

Digunakan untuk memastikan user pertama dapat melihat data yang tersimpan pada tabel `informasi`.

Jika data berhasil ditampilkan, berarti login dan hak akses user pertama berhasil. Keluar menggunakan:

```sql
EXIT;
```

## 12. Pengujian Login User Kedua

Pada terminal Ubuntu, jalankan:

```bash
mariadb -u ukomtkj-2 -p ukom3
```

Digunakan untuk login ke database `ukom3` menggunakan akun kedua, yaitu `ukomtkj-2`. Masukkan password ketika diminta.

Setelah berhasil masuk, jalankan:

```sql
SELECT * FROM informasi;
```

Digunakan untuk memastikan user kedua juga dapat melihat data pada tabel `informasi`.

Jika data berhasil ditampilkan, berarti login dan hak akses user kedua berhasil. Keluar dari konsol menggunakan:

```sql
EXIT;
```

## 13. Kesimpulan

Pada tahap ini, saya berhasil mengatur IP Address Ubuntu Server agar terhubung dengan jaringan MikroTik. Selanjutnya, saya melakukan instalasi dan konfigurasi MariaDB dengan membuat database `ukom3`, dua akun pengguna, serta memberikan hak akses kepada masing-masing akun.

Saya juga berhasil membuat tabel `informasi`, memasukkan beberapa data, dan melakukan pengujian login menggunakan kedua user. Hasil pengujian menunjukkan bahwa kedua akun dapat mengakses database dan menampilkan data yang tersimpan.
