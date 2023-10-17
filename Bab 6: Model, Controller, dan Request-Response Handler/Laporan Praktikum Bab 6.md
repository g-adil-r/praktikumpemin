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
       <i>Gambar 1.1: Nama-nama kolom yang terdapat pada tabel user</i>
    </p>

2. Bersihkan isi `User.php` yang ada sebelumnya dan isi dengan baris kode berikut

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

---
## Controller
1. Buatlah salinan `ExampleController.php` pada folder `app/Http/Controllers` dengan nama `HomeController.php` dan buatlah fungsi `index()` yang berisi

    ```php
    <?php

    namespace App\Http\Controllers;

    class HomeController extends Controller
    {
        /**
         * Create a new controller instance.
         *
         * @return void
         */
        public function __construct()
        {
            //
        }

        // Pembuatan fungsi index() //
        public function index()
        {
            return 'Hello, from lumen!';
        }
        // Pembuatan fungsi index() //

        //
    }
    ```

    <p align="center">
       <img src="pic/ss2-01.png" width=500></img><br>
       <i>Gambar 2.1: Membuat controller baru bernama HomeController</i>
    </p>

2. Ubah route `/` pada file `routes/web.php` menjadi seperti ini

    ```php
    # Sebelum,
    $router->get('/', function () use ($router) {
        return $router->app->version();
    });
    # Setelah,
    $router->get('/', ['uses' => 'HomeController@index']);
    ```

    <p align="center">
       <img src="pic/ss2-02.png" width=400></img><br>
       <i>Gambar 2.2: Memodifikasi route <code>/</code> sehingga menggunakan HomeController</i>
    </p>

3. Jalankan aplikasi

    <p align="center">
       <img src="pic/ss2-03.png" width=300></img><br>
       <i>Gambar 2.3: Menjalankan aplikasi lumenapi</i>
    </p>

---
## Request Handler
1. Lakukan import library Request dengan menambahkan baris berikut di bagian atas file

    ```php
    <?php

    namespace App\Http\Controllers;
    // Import Library Request
    use Illuminate\Http\Request;
    ```

    <p align="center">
       <img src="pic/ss3-01.png" width=300></img><br>
       <i>Gambar 3.1: Import library Request pada HomeController</i>
    </p>

2. Ubah fungsi index menjadi

    ```php
    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;

    class HomeController extends Controller
    {
        /**
         * Create a new controller instance.
         *
         * @return void
         */
        public function __construct()
        {
            //
        }

        // Perubahan fungsi index
        public function index (Request $request)
        {
            return 'Hello, from lumen! We got your request from endpoint: ' . $request->path();
        }
        // Perubahan fungsi index
        
        //
    }
    ```

    <p align="center">
       <img src="pic/ss3-02.png" width=600></img><br>
       <i>Gambar 3.2: Mengubah fungsi <code>index()</code> pada HomeController</i>
    </p>

3. Jalankan aplikasi

    <p align="center">
       <img src="pic/ss3-03.png" width=300></img><br>
       <i>Gambar 3.3: Menjalan aplikasi lumenapi setelah mengubah fungsi <code>index()</code> pada HomeController</i>
    </p>

---
## Response Handler

1. Lakukan import library Response dengan menambahkan baris berikut di bagian atas file

    ```php
    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;
    use Illuminate\Http\Response; // import library Response
    ```

    <p align="center">
       <img src="pic/ss4-01.png" width=350></img><br>
       <i>Gambar 4.1: Import library Response pada HomeController</i>
    </p>

2. Buatlah fungsi hello() yang berisi

    ```php
    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;
    use Illuminate\Http\Response;

    class HomeController extends Controller
    {
        /**
         * Create a new controller instance.
         *
         * @return void
         */
        public function __construct()
        {
            //
        }

        public function index (Request $request)
        {
        return 'Hello, from lumen! We got your request from endpoint: ' . $request->path();
        }

        //

        // Pembuatan fungsi hello
        public function hello()
        {
            $data['status'] = 'Success';
            $data['message'] = 'Hello, from lumen!';
            return (new Response($data, 201))
                ->header('Content-Type', 'application/json');
        }
    }
    ```

    <p align="center">
       <img src="pic/ss4-02.png" width=450></img><br>
       <i>Gambar 4.2: Menambahkan fungsi <code>hello()</code> pada HomeController</i>
    </p>

3. Tambahkan route `/hello` pada file routes/web.php

    ```php
    <?php

    ...

    $router->get('/', ['uses' => 'HomeController@index']);
    $router->get('/hello', ['uses' => 'HomeController@hello']); // route hello
    ```

    <p align="center">
       <img src="pic/ss4-03.png" width=450></img><br>
       <i>Gambar 4.3: Menambahkan route <code>/hello</code> pada route</i>
    </p>

4. Jalankan aplikasi pada route `/hello`

    <p align="center">
       <img src="pic/ss4-04.png" width=300></img><br>
       <i>Gambar 4.4: Menjalankan aplikasi lumenapi pada route <code>/hello</code></i>
    </p>