# **BAB 6: Relasi One-to-Many dan Many-to-Many**
**Oleh Ghifari Adil Ruchiyat - 215150701111003**

## Pembuatan Tabel
Berikut adalah tabel yang akan digunakan pada percobaan ini

| posts             | comments        | tags | post_tag |
|-------------------|-----------------|------|----------|
| id                | id              | id   | postId   |
| content (STRING)  | review (STRING) | name | tagId    |

1. Sebelum membuat migrasi database atau membuat tabel pastikan server database aktif kemudian pastikan sudah membuat database dengan nama `lumenpost`

    <p align="center">
       <img src="pic/ss1-01.png" width=250></img><br>
       <i>Gambar 1.1: Membuat database <code>lumenpost</code> </i>
    </p>

2. Kemudian ubah konfigurasi database pada file .env menjadi seperti berikut

    ```
    DB_CONNECTION=mysql
    DB_HOST=127.0.0.1
    DB_PORT=3306
    DB_DATABASE=lumenpost
    DB_USERNAME=root
    DB_PASSWORD=
    ```

    <p align="center">
       <img src="pic/ss1-02.png" width=250></img><br>
       <i>Gambar 1.2: Mengubah konfigurasi pada file <code>.env</code> </i>
    </p>

3. Setelah mengubah konfigurasi pada file .env, kita juga perlu menghidupkan beberapa library bawaan dari lumen dengan membuka file app.php pada folder bootstrap dan mengubah baris ini

    ```php
    // $app->withFacades();
    // $app->withEloquent();
    ```

    menjadi

    ```php
    $app->withFacades();
    $app->withEloquent();
    ```

    <p align="center">
       <img src="pic/ss1-03.png" width=400></img><br>
       <i>Gambar 1.3: Menghidupkan library Facades dan Eloquent</i>
    </p>

4. Setelah itu jalankan command berikut untuk membuat file migration

    ```
    php artisan make:migration create_posts_table
    php artisan make:migration create_comments_table
    php artisan make:migration create_tags_table
    php artisan make:migration create_post_tag_table
    ```

    <p align="center">
       <img src="pic/ss1-04.png" width=600></img><br>
       <i>Gambar 1.4: Membuat file migration</i>
    </p>

5. Ubah fungsi `up()` pada file migrasi `create_posts_table`

    ```php
    #sebelumnya
    ...
    public function up()
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->timestamps();
        });
    }
    ...

    #diubah menjadi
    ...
    public function up()
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->timestamps();
            $table->string('content');
        });
    }
    ...
    ```

    <p align="center">
       <img src="pic/ss1-05.png" width=400></img><br>
       <i>Gambar 1.5: Mengubah isi file migration <code>create_posts_table</code> </i>
    </p>

6. Ubah fungsi `up()` pada file `create_comments_table`

    ```php
    #sebelumnya
    ...
    public function up()
    {
        Schema::create('comments', function (Blueprint $table) {
            $table->id();
            $table->timestamps();
        });
    }
    ...

    #diubah menjadi
    ...
    public function up()
    {
        Schema::create('comments', function (Blueprint $table) {
            $table->id();
            $table->timestamps();
            $table->string('review');
            $table->foreignId('postId')->unsigned();
        });
    }
    ...
    ```

    <p align="center">
       <img src="pic/ss1-06.png" width=400></img><br>
       <i>Gambar 1.6: Mengubah isi file migration <code>create_comments_table</code> </i>
    </p>

7. Ubah fungsi `up()` pada file `create_tags_table`

    ```php
    #sebelumnya
    ...
    public function up()
    {
        Schema::create('tags', function (Blueprint $table) {
            $table->id();
            $table->timestamps();
        });
    }
    ...

    #diubah menjadi
    ...
    public function up()
    {
        Schema::create('tags', function (Blueprint $table) {
            $table->id();
            $table->timestamps();
            $table->string('name');
        });
    }
    ...
    ```

    <p align="center">
       <img src="pic/ss1-07.png" width=400></img><br>
       <i>Gambar 1.7: Mengubah isi file migration <code>create_tags_table</code> </i>
    </p>

8. Ubah fungsi `up()` pada file `create_post_tag_table`

    ```php
    #sebelumnya
    ...
    public function up()
    {
        Schema::create('post_tag', function (Blueprint $table) {
            $table->id();
            $table->timestamps();
        });
    }
    ...

    #diubah menjadi
    ...
    public function up()
    {
        Schema::create('post_tag', function (Blueprint $table) {
            $table->id();
            $table->timestamps();
            $table->foreignId('postId')->unsigned();
            $table->foreignId('tagId')->unsigned();
        });
    }
    ...
    ```

    <p align="center">
       <img src="pic/ss1-07.png" width=400></img><br>
       <i>Gambar 1.7: Mengubah isi file migration <code>create_post_tag_table</code> </i>
    </p>

9. Kemudian jalankan command

    ```
    php artisan migrate
    ```

    <p align="center">
       <img src="pic/ss1-09.png" width=600></img><br>
       <i>Gambar 1.9: Melakukan migrasi tabel ke database</i>
    </p>