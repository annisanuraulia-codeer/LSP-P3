# B. INSTALASI WEB SERVER APACHE DAN PHP

Pada tahap ini, saya melakukan instalasi Apache dan PHP pada Ubuntu Server. Apache digunakan untuk menjalankan layanan web server, sedangkan PHP digunakan untuk memproses halaman website dan menghubungkannya dengan database MariaDB yang sudah dibuat pada tahap sebelumnya.

## 1. Menginstal Apache dan PHP

Langkah pertama adalah memperbarui dan memasang paket yang dibutuhkan untuk menjalankan website.

Jalankan perintah berikut:

```bash
sudo apt install apache2 php libapache2-mod-php php-mysql -y
```

**Penjelasan:**

- `apache2` merupakan layanan web server yang bertugas menerima permintaan dari browser dan menampilkan halaman website.
- `php` digunakan untuk menjalankan kode PHP pada server.
- `libapache2-mod-php` berfungsi menghubungkan PHP dengan Apache agar file PHP dapat diproses.
- `php-mysql` menyediakan dukungan agar PHP dapat terhubung ke database MariaDB.
- `-y` digunakan untuk menyetujui proses instalasi secara otomatis.

## 2. Mengaktifkan dan Memeriksa Layanan Apache

Setelah proses instalasi selesai, aktifkan layanan Apache menggunakan perintah:

```bash
sudo systemctl enable --now apache2
```

Perintah `enable` membuat Apache otomatis aktif ketika Ubuntu Server dinyalakan, sedangkan `--now` langsung menjalankan layanan saat itu juga.

Untuk memeriksa apakah Apache sudah berjalan, gunakan:

```bash
sudo systemctl status apache2
```

Pastikan status layanan menunjukkan **`active (running)`**. Jika sudah muncul, berarti Apache berhasil dijalankan. Tekan `q` untuk keluar dari tampilan status.

## 3. Membuat Halaman Utama Website

Apache memiliki direktori khusus untuk menyimpan file website, yaitu `/var/www/html`.

Buka file halaman utama menggunakan perintah:

```bash
sudo nano /var/www/html/index.html
```

File `index.html` digunakan sebagai halaman utama yang akan ditampilkan ketika client mengakses alamat website melalui browser.

Pada tahap ini, isi halaman HTML dapat dimasukkan sesuai desain website yang sudah disiapkan. Setelah selesai mengedit, simpan menggunakan `Ctrl + O`, tekan `Enter`, kemudian keluar dengan `Ctrl + X`.

## 4. Membuat Halaman untuk Menampilkan Data Database

Selanjutnya, buat file PHP yang berfungsi menghubungkan website dengan database MariaDB.

Jalankan perintah:

```bash
sudo nano /var/www/html/database.php
```

Masukkan kode berikut:

```php
<?php
$host = "localhost";
$user = "ukomtkj-1";
$password = "UkomDB@2026";
$database = "ukom3";

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

**Penjelasan bagian kode:**

- `$host = "localhost"` menunjukkan bahwa database berada pada server yang sama dengan Apache.
- `$user` berisi nama akun MariaDB yang digunakan untuk mengakses database.
- `$password` berisi password dari user database tersebut.
- `$database` menentukan database yang akan digunakan, yaitu `ukom3`.
- `new mysqli()` digunakan untuk membuat koneksi antara PHP dan MariaDB.
- `connect_error` digunakan untuk memeriksa apakah koneksi database mengalami kesalahan.
- `set_charset("utf8mb4")` mengatur format karakter agar teks dapat ditampilkan dengan baik.
- `SELECT` digunakan untuk mengambil data dari tabel `informasi`.
- `fetch_assoc()` mengambil data setiap baris agar dapat ditampilkan melalui halaman website.
- `htmlspecialchars()` membantu mengamankan tampilan teks agar karakter khusus tidak dibaca sebagai kode HTML.
- `$conn->close()` digunakan untuk menutup koneksi database setelah proses selesai.

Dengan kode tersebut, halaman `database.php` akan menampilkan data yang sebelumnya sudah dimasukkan ke tabel `informasi` pada Bagian A.

Setelah selesai memasukkan kode, simpan menggunakan `Ctrl + O`, tekan `Enter`, lalu keluar dengan `Ctrl + X`.

## 5. Memulai Ulang Layanan Apache

Setelah file website dan PHP selesai dibuat, jalankan perintah:

```bash
sudo systemctl restart apache2
```

Perintah ini digunakan untuk memulai ulang layanan Apache agar perubahan konfigurasi dan file yang dibuat dapat diterapkan.

## 6. Pengujian Website melalui Browser

Setelah Apache dan PHP berjalan, lakukan pengujian melalui browser pada laptop client yang terhubung ke jaringan server.

**A. Menguji Halaman Utama**

```text
http://ukom12tkj-3.sch.id/
```

Alamat tersebut digunakan untuk mengakses halaman utama website yang tersimpan pada file `index.html`.

**B. Menguji Koneksi Database**

```text
http://ukom12tkj-3.sch.id/database.php
```

Alamat tersebut digunakan untuk membuka halaman PHP yang menghubungkan website dengan database MariaDB.

**C. Pengujian Menggunakan IP Address**

Jika konfigurasi DNS domain belum selesai, pengujian sementara dapat dilakukan menggunakan IP Ubuntu Server:

```text
http://100.100.3.2/
```

Untuk halaman database:

```text
http://100.100.3.2/database.php
```

Jika halaman utama berhasil ditampilkan dan halaman database menampilkan pesan **Koneksi Database Berhasil** beserta data informasi, berarti Apache, PHP, dan koneksi MariaDB sudah berjalan dengan baik.

## 7. Kesimpulan

Pada tahap ini, saya berhasil melakukan instalasi dan konfigurasi Apache serta PHP pada Ubuntu Server. Apache digunakan untuk menjalankan layanan web server, sedangkan PHP berfungsi memproses halaman website dan menghubungkannya dengan database MariaDB.

Selanjutnya, dibuat halaman utama website dan halaman PHP untuk menampilkan data dari database `ukom3`. Pengujian dilakukan melalui browser menggunakan domain dan IP Address Ubuntu Server, yaitu `100.100.3.2`, untuk memastikan website dapat diakses serta koneksi database berhasil dilakukan.
