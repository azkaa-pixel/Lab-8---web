# Lab-8-web

Nama : Ghefira Azka Fardani 

NIM : 312410521

Kelas : TI.24.A5

### 1. Koneksi Database (`koneksi.php`)
Membuat file untuk menghubungkan PHP ke database MySQL menggunakan `mysqli_connect()`.
```html
<?php
$host     = "localhost";
$user     = "root";
$password = "";
$database = "latihan 1";

$conn = mysqli_connect($host, $user, $password, $database);

if (!$conn) {
    die("Koneksi gagal: " . mysqli_connect_error());
}
?>
```

#### ```- penjelasan``` : 
- Database yang digunakan bernama `latihan 1` (pakai spasi sesuai input user)

- Variabel koneksi disimpan dalam `$conn`

- Jika gagal, script akan menghentikan proses `(die())`

### 2. Halaman Utama `(index.php)`
```html
<?php 
include 'koneksi.php';
$barang = mysqli_query($conn, "SELECT * FROM data_barang");
?>

<!DOCTYPE html>
<html>
<head>
  <link href="style.css" rel="stylesheet">
  <title>Data Barang</title>
</head>
<body>

<h2>Daftar Barang</h2>
<a href="tambah.php" class="btn">Tambah Barang</a>

<table>
  <tr>
    <th>ID</th>
    <th>Gambar</th>
    <th>Nama</th>
    <th>Harga</th>
    <th>Stok</th>
    <th>Aksi</th>
  </tr>

  <?php while($row = mysqli_fetch_assoc($barang)) : ?>
  <tr>
    <td><?= $row['id'] ?></td>
    <td><img src="gambar/<?= $row['gambar'] ?>" width="60"></td>
    <td><?= $row['nama'] ?></td>
    <td><?= $row['harga'] ?></td>
    <td><?= $row['stok'] ?></td>
    <td>
      <a href="ubah.php?id=<?= $row['id'] ?>" class="edit">Edit</a>
      <a href="hapus.php?id=<?= $row['id'] ?>" class="delete">Hapus</a>
    </td>
  </tr>
  <?php endwhile; ?>
</table>

</body>
</html>
```

#### ```- penjelasan``` : 
- Mengambil data menggunakan `mysqli_fetch_assoc`

- Tombol aksi untuk edit dan hapus

- Menampilkan gambar dari folder `/gambar`

### 3. Menambah Data (`tambah.php`)
```html
<?php include 'koneksi.php'; ?>

<!DOCTYPE html>
<html>
<head>
  <link href="style.css" rel="stylesheet">
  <title>Tambah Barang</title>
</head>
<body>

<h2>Tambah Barang</h2>

<form action="" method="POST" enctype="multipart/form-data">
  <label>Nama:</label>
  <input type="text" name="nama" required>

  <label>Harga:</label>
  <input type="number" name="harga" required>

  <label>Stok:</label>
  <input type="number" name="stok" required>

  <label>Upload Gambar:</label>
  <input type="file" name="gambar" required>

  <button type="submit" name="submit">Simpan</button>
</form>

<?php
if (isset($_POST['submit'])) {
    $nama  = $_POST['nama'];
    $harga = $_POST['harga'];
    $stok  = $_POST['stok'];
    $gambar = $_FILES['gambar']['name'];
    $tmp    = $_FILES['gambar']['tmp_name'];

    move_uploaded_file($tmp, "gambar/" . $gambar);

    mysqli_query($conn, "INSERT INTO data_barang VALUES('', '$gambar', '$nama', '$harga', '$stok')");
    echo "<script>alert('Data berhasil ditambahkan!');window.location='index.php'</script>";
}
?>

</body>
</html>
```

#### ```- penjelasan``` : 
- Upload file disimpan ke folder `gambar/`

- Query insert menggunakan `mysqli_query()`

### 4. Mengubah Data (`ubah.php`)
```html
<?php 
include 'koneksi.php';
$id = $_GET['id'];
$data = mysqli_query($conn, "SELECT * FROM data_barang WHERE id='$id'");
$row = mysqli_fetch_assoc($data);
?>

<!DOCTYPE html>
<html>
<head>
  <link href="style.css" rel="stylesheet">
  <title>Ubah Barang</title>
</head>
<body>

<h2>Ubah Barang</h2>

<form action="" method="POST" enctype="multipart/form-data">
  <label>Nama:</label>
  <input type="text" name="nama" value="<?= $row['nama'] ?>" required>

  <label>Harga:</label>
  <input type="number" name="harga" value="<?= $row['harga'] ?>" required>

  <label>Stok:</label>
  <input type="number" name="stok" value="<?= $row['stok'] ?>" required>

  <label>Gambar Baru:</label>
  <input type="file" name="gambar">
  
  <button type="submit" name="submit">Update</button>
</form>

<?php
if(isset($_POST['submit'])){
    $nama  = $_POST['nama'];
    $harga = $_POST['harga'];
    $stok  = $_POST['stok'];

    if ($_FILES['gambar']['name'] != "") {
        $gambar = $_FILES['gambar']['name'];
        move_uploaded_file($_FILES['gambar']['tmp_name'], "gambar/" . $gambar);
    } else {
        $gambar = $row['gambar'];
    }

    mysqli_query($conn, "UPDATE data_barang SET gambar='$gambar', nama='$nama', harga='$harga', stok='$stok' WHERE id='$id'");
    echo "<script>alert('Data berhasil diubah!');window.location='index.php'</script>";
}
?>

</body>
</html>
```

#### ```- penjelasan``` : 
- Jika user tidak upload gambar baru, gambar lama tetap digunakan

- Update berdasarkan id

### 5. Menghapus Data (`hapus.php`)
```html
<?php
include 'koneksi.php';
$id = $_GET['id'];

mysqli_query($conn, "DELETE FROM data_barang WHERE id='$id'");
echo "<script>alert('Data berhasil dihapus!');window.location='index.php'</script>";
?>
```

#### ```- penjelasan``` : 
- Menghapus berdasarkan `id`

- Setelah hapus redirect otomatis ke `index.php`
### 6. css style
```html
body {
  font-family: -apple-system, BlinkMacSystemFont, "San Francisco", Arial;
  background-color: #f7f8fa;
  padding: 20px;
  color: #1c1c1e;
}

table {
  width: 100%;
  border-collapse: collapse;
  background: white;
  border-radius: 16px;
  overflow: hidden;
  box-shadow: 0 5px 15px rgba(0,0,0,.08);
  transition: .4s ease;
}

table:hover {
  transform: scale(1.01);
}

th {
  background: #007aff;
  color: white;
  padding: 12px;
  text-align: left;
}

td {
  border-bottom: 1px solid #e5e5ea;
  padding: 12px;
}

td img {
  border-radius: 12px;
}

a.edit {
  color: #007aff;
}

a.delete {
  color: red;
}

input, button {
  width: 100%;
  padding: 10px;
  margin-bottom: 12px;
  border-radius: 10px;
  border: 1px solid #d1d1d6;
}

button {
  background: #007aff;
  color: white;
  border: none;
}
```
#### ```- penjelasan``` : 
- Font menggunakan sistem Apple (`San Francisco`)

- Tabel diberi bayangan + hover zoom

- Warna tombol biru khas iOS
