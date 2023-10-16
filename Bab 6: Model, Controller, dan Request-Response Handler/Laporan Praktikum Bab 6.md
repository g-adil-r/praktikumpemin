# **BAB 6: Model, Controller, dan Request-Response Handler**
**Oleh Ghifari Adil Ruchiyat - 215150701111003**

## Model

1. Pastikan terdapat tabel users yang dibuat menggunakan migration pada bab sebelumnya. Berikut informasi kolom yang harus ada

    * id
    * createdAt
    * updatedAt
    * name
    * email
    * password

    <p align="center">
       <img src="pic/ss1-01.png" width=400></img><br>
       <i>Gambar 1.1: Gambar kolom pada tabel user</i>
    </p>

2. Bersihkan isi User.php yang ada sebelumnya dan isi dengan baris kode berikut

    ```php
    <?php

    namespace App\Models;

    use Illuminate\Database\Eloquent\Model;

    class User extends Model
    {
        /**
         * The attributes that are mass assignable.
         *
         * @var array
         */
        protected $fillable = [
            'name', 'email', 'password'
        ];
        /**
         * The attributes excluded from the model's JSON form.
         *
         * @var array
         */
        protected $hidden = [];
    }
    ```

    <p align="center">
       <img src="pic/ss1-02.png" width=500></img><br>
       <i>Gambar 1.2: Mengubah model untuk user</i>
    </p>