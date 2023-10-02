# **BAB 4: Basic Routing dan Migration**
**Oleh Ghifari Adil Ruchiyat - 215150701111003**

1. **GET**

   Untuk menambahkan endpoint dengan method GET pada aplikasi kita, kita dapat mengunjungi file web.php pada folder routes. Kemudian tambahkan baris ini pada akhir file

   ```php
    ...
    $router->get('/get', function () {
        return 'GET';
    });
   ```
   <p align="center">
      <img src="pic/ss1-01-1.png" width=600></img><br>
      <i>Gambar 1.1: Menambahkan kode pada file web.php untuk route /get</i>
   </p>

   Setelah itu coba jalankan aplikasi dengan command

   ```powershell
   php -S localhost:8000 -t public
   ```

   > Note: Pastikan buka cmd pada folder aplikasi

   <p align="center">
      <img src="pic/ss1-01-2.png" width=600></img><br>
      <i>Gambar 1.2: Menjalankan aplikasi lewat cmd</i>
   </p>

   Setelah aplikasi berhasil dijalankan, kita dapat membuka browser dengan url, http://localhost:8000/get, path yang akan kita akses akan berbentuk demikian, http://{BASE_URL}{PATH}, jika BASE_URL kita adalah localhost:8000 dan PATH kita adalah /get, maka url akan berbentuk seperti diatas.
   
   <p align="center">
      <img src="pic/ss1-01-3.png" width=300></img><br>
      <i>Gambar 1.3: Membuka aplikasi dari browser dengan url</i>
   </p>

2. **POST, PUT, PATCH, DELETE, dan OPTIONS**

    Sama halnya saat menambahkan method GET, kita dapat menambahkan method POST, PUT, PATCH, DELETE, dan OPTIONS pada file web.php dengan code seperti ini

    ```php
    ...
    $router->post('/post', function () {
        return 'POST';
    });
    $router->put('/put', function () {
        return 'PUT';
    });
    $router->patch('/patch', function () {
        return 'PATCH';
    });
    $router->delete('/delete', function () {
        return 'DELETE';
    });
    $router->options('/options', function () {
        return 'OPTIONS';
    });
    ```

    <p align="center">
       <img src="pic/ss1-02-1.png" width=450></img><br>
       <i>Gambar 1.4: Menambahkan route /post, /put, /patch, /delete, dan /options</i>
    </p>

    Setelah selesai menambahkan route untuk method POST, PUT, PATCH, DELETE, dan OPTIONS, kita dapat menjalankan server seperti pada saat percobaan GET. Setelah server berhasil menyala, kita dapat membuka aplikasi Postman atau Insomnia atau kita juga dapat menggunakan PowerShell (Windows) / Terminal (Linux atau Mac) untuk melakukan request ke server. Namun, pada percobaan kali ini kita akan menggunakan extensions pada VSCode yaitu Thunder Client.

    1. Kita dapat menginstall ekstensi dengan membuka panel extensions lalu mencari thunder client
       
        <p align="center">
           <img src="pic/ss1-02-a.png" width=600></img><br>
           <i>Gambar 1.5: Thunder client pada VSCode</i>
        </p>
    
    2. Setelah menginstall Thunder Client, kita akan melihat logo seperti petir pada activity bar kita (sebelah kiri).

        <p align="center">
           <img src="pic/ss1-02-b.png" width=300></img><br>
           <i>Gambar 1.6: Logo thunder client activity bar di VSCode</i>
        </p>
    
    3. Kita dapat membuat request dengan menekan "New Request" pada ekstensi

        <p align="center">
           <img src="pic/ss1-02-c.png" width=600></img><br>
           <i>Gambar 1.7: Tampilan awal extension thunder client</i>
        </p>
    
    4. Setelah itu kita dapat memasukkan method dan url yang dituju

        <p align="center">
           <img src="pic/ss1-02-d.png" width=450></img><br>
           <i>Gambar 1.8: Memasukkan method dan url yang dituju pada thunder client</i>
        </p>
    
    5. Akses url yang baru saja ditambahkan pada aplikasi dengan methodnya

        <p align="center">
           <img src="pic/ss1-02-e.png" width=600></img><br>
           <i>Gambar 1.9: Mengakses salah satu endpoint aplikasi menggunakan thunder client</i>
        </p>