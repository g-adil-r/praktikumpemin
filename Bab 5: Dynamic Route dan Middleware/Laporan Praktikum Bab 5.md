# **BAB 5: Dynamic Route dan Middleware**
**Oleh Ghifari Adil Ruchiyat - 215150701111003**

1. **Dynamic Route**
    Dynamic route adalah route yang dapat berubah-ubah, contohnya pada saat kita membuka suatu halaman web, kadang kita melihat /users/1 atau /users/2 , hal ini yang dinamakan dynamic routes.
    
    Untuk menambahkan dynamic routes pada aplikasi lumen kita, kita dapat menggunakan syntax berikut,
    
    ```php
    $router->get('/user/{id}', function ($id) {
        return 'User Id = ' . $id;
    });
    ```

    <p align="center">
       <img src="pic/ss1-01.png" width=500></img><br>
       <i>Gambar 1.1: Membuat dynamic route </i>
    </p>

    <p align="center">
       <img src="pic/ss1-02.png" width=500></img><br>
       <i>Gambar 1.2: Hasil request menggunakan dynamic route </i>
    </p>

    Saat menambahkan parameter pada routes, kita tidak terbatas pada 1 variable saja, namun kita dapat menambahkan sebanyak yang diperlukan seperti kode berikut,

    ```php
    $router->get('/post/{postId}/comments/{commentId}', function ($postId, $commentId) {
        return 'Post ID = ' . $postId . ' Comments ID = ' . $commentId;
    });
    ```

    <p align="center">
       <img src="pic/ss1-03.png" width=600></img><br>
       <i>Gambar 1.3: Membuat dynamic route dengan banyak parameter </i>
    </p>

    <p align="center">
       <img src="pic/ss1-04.png" width=600></img><br>
       <i>Gambar 1.4: Hasil request menggunakan dynamic route dengan banyak parameter</i>
    </p>

    Pada dynamic routes kita juga bisa menambahkan optional routes, yang mana optional routes tidak mengharuskan kita untuk memberi variable pada endpoint kita, namun saat kita memanggil endpoint, dapat menggunakan parameter variable ataupun tidak, seperti pada kode dibawah ini,

    ```php
    $router->get('/users[/{userId}]', function ($userId = null) {
        return $userId === null ? 'Data semua users' : 'Data user dengan id ' . $userId;
    });
    ```

    <p align="center">
       <img src="pic/ss1-05.png" width=600></img><br>
       <i>Gambar 1.5: Membuat dynamic route dengan optional routes</i>
    </p>

    <p align="center">
       <img src="pic/ss1-06.png" width=500></img><br>
       <i>Gambar 1.6: Hasil request endpoint <code>/users</code> menggunakan optional routes </i>
    </p>

    <p align="center">
       <img src="pic/ss1-07.png" width=500></img><br>
       <i>Gambar 1.7: Hasil request endpoint <code>/users/123</code> menggunakan optional routes</i>
    </p>