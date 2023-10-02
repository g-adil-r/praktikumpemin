# **BAB 4: Basic Routing dan Migration**
**Oleh Ghifari Adil Ruchiyat - 215150701111003**

1. GET

   Untuk menambahkan endpoint dengan method GET pada aplikasi kita, kita dapat mengunjungi file web.php pada folder routes. Kemudian tambahkan baris ini pada akhir file

   ```php
    ...
    $router->get('/get', function () {
        return 'GET';
    });
   ```
   <p align="center">
      <img src="pic/ss1-01-1.png" width=600></img><br>
      <i>Gambar 1.1: Menambahakn kode pada file web.php untuk menambah endpoint</i>
   </p>

   Setelah itu coba jalankan aplikasi dengan command

   ```powershell
   php -S localhost:8000 -t public
   ```

   ># Note: Pastikan buka cmd pada folder aplikasi

   <p align="center">
      <img src="pic/ss1-01-2.png" width=600></img><br>
      <i>Gambar 1.1: Menambahakn kode pada file web.php untuk menambah endpoint</i>
   </p>
