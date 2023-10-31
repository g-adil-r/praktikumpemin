# **BAB 8: Register, Authentication dan Authorization**
**Oleh Ghifari Adil Ruchiyat - 215150701111003**

## Register
1. Pastikan terdapat tabel users yang dibuat menggunakan migration pada bab 3 Basic Routing dan Migration. Berikut informasi kolom yang harus ada:

    |           |
    |-----------|
    | id        |
    | createdAt |
    | updatedAt |
    | name      |
    | email     |
    | password  |

    <p align="center">
       <img src="pic/ss1-01.png" width=400></img><br>
       <i>Gambar 1.1: Tabel users dan kolomnya</i>
    </p>

2. Pastikan terdapat model User.php yang digunakan pada bab 5 Model, Controller dan Request-Response Handler. Berikut baris kode yang harus ada:

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
       <img src="pic/ss1-02.png" width=400></img><br>
       <i>Gambar 1.2: Kode dari model user</i>
    </p>

3. Buatlah file AuthController.php dan isilah dengan baris kode berikut

    ```php
    <?php

    namespace App\Http\Controllers;

    use App\Models\User;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Hash;

    class AuthController extends Controller
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

        //
        public function register(Request $request)
        {
            $name = $request->name;
            $email = $request->email;
            $password = Hash::make($request->password);

            $user = User::create([
                'name' => $name,
                'email' => $email,
                'password' => $password
            ]);

            return response()->json([
                'status' => 'Success',
                'message' => 'new user created',
                'data' => [
                    'user' => $user,
                ]
            ],200);
        }
    }
    ```

    <p align="center">
       <img src="pic/ss1-03.png" width=400></img><br>
       <i>Gambar 1.3: Membuat controller baru bernama <code>AuthController.php</code> </i>
    </p>

4. Tambahkan baris berikut pada routes/web.php

    ```php
    <?php

    ...

    $router->group(['prefix' => 'auth'], function () use ($router) {
        $router->post('/register', ['uses'=> 'AuthController@register']);
    });
    ```

    <p align="center">
       <img src="pic/ss1-04.png" width=450></img><br>
       <i>Gambar 1.4: Membuat endpoint baru untuk registrasi</i>
    </p>

5. Jalankan aplikasi pada endpoint /auth/register dengan body berikut

    ```JSON
    {
        "name": "Scaramouche",
        "email": "scaramouche@fatui.org",
        "password": "wanderer"
    }
    ```

    <p align="center">
       <img src="pic/ss1-05.png" width=500></img><br>
       <i>Gambar 1.5: Melakukan registrasi user baru dengan endpoint <code>/auth/register</code> </i>
    </p>

---

## Authentication
1. Buatlah fungsi `login(Request $request)` pada file AuthController.php

    ```php
    <?php

    namespace App\Http\Controllers;

    use App\Models\User;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Hash;

    class AuthController extends Controller
    {
        ...

        public function login(Request $request)
        {
            $email = $request->email;
            $password = $request->password;

            $user = User::where('email', $email)->first();

            if (!$user) {
                return response()->json([
                    'status' => 'Error',
                    'message' => 'user not exist',
                ],404);
            }

            if (!Hash::check($password, $user->password)) {
                return response()->json([
                    'status' => 'Error',
                    'message' => 'wrong password',
                ],400);
            }

            return response()->json([
                'status' => 'Success',
                'message' => 'successfully login',
                'data' => [
                    'user' => $user,
                ]
            ],200);
        }
    }
    ```

    <p align="center">
       <img src="pic/ss2-01.png" width=450></img><br>
       <i>Gambar 2.1: Menambahkan fungsi login pada AuthController</i>
    </p>

2. Tambahkan baris berikut pada routes/web.php

    ```php
    <?php

    ...

    $router->group(['prefix' => 'auth'], function () use ($router) {
        $router->post('/register', ['uses'=> 'AuthController@register']);
        $router->post('/login', ['uses'=> 'AuthController@login']); // route login
    });
    ```

    <p align="center">
       <img src="pic/ss2-02.png" width=450></img><br>
       <i>Gambar 2.2: Menambahkan endpoint baru untuk login </i>
    </p>

3. Jalankan aplikasi pada endpoint /auth/login dengan body berikut

    ```JSON
    {
        "email": "scaramouche@fatui.org",
        "password": "wanderer"
    }
    ```

    <p align="center">
       <img src="pic/ss2-03.png" width=450></img><br>
       <i>Gambar 2.3: Melakukan login dengan endpoint <code>/auth/login</code> </i>
    </p>

4. Lakukan percobaan dengan menyalahkan email atau password dan amati responnya.

    <p align="center">
       <img src="pic/ss2-04.png" width=400></img><br>
       <i>Gambar 2.4: Melakukan login dengan kredensial yang salah </i>
    </p>

---
## Token
1. Jalankan perintah berikut untuk membuat migrasi baru

    ```
    php artisan make:migration add_column_token_to_users
    ```

    <p align="center">
       <img src="pic/ss3-01.png" width=450></img><br>
       <i>Gambar 3.1: Membuat migrasi baru untuk menambahkan kolom token ke tabel user </i>
    </p>

2. Tambahkan baris berikut pada migration yang baru terbuat

    ```php
    <?php

    use Illuminate\Database\Migrations\Migration;
    use Illuminate\Database\Schema\Blueprint;
    use Illuminate\Support\Facades\Schema;

    class AddColumnTokenToUsers extends Migration
    {
        /**
         * Run the migrations.
         *
         * @return void
         */
        public function up()
        {
            Schema::table('users', function (Blueprint $table) {
                $table->string('token', 72)->unique()->nullable(); //
            });
        }

        /**
         * Reverse the migrations.
         *
         * @return void
         */
        public function down()
        {
            Schema::table('users', function (Blueprint $table) {
                $table->dropIfExists('token'); //
            });
        }
    }
    ```

    <p align="center">
       <img src="pic/ss3-02.png" width=450></img><br>
       <i>Gambar 3.2: Menulis kode untuk menambahkan kolom token</i>
    </p>

3. Tambahkan atribut token di `$fillable` pada User.php

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
            'name', 'email', 'password',
            'token' //
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
       <img src="pic/ss3-03.png" width=450></img><br>
       <i>Gambar 3.3: Menambahkan atribut token pada model user</i>
    </p>

4. Tambahkan baris berikut pada file AuthController.php

    ```php
    <?php

    namespace App\Http\Controllers;

    use App\Models\User;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Hash;
    use Illuminate\Support\Str;

    class AuthController extends Controller
    {
        ...

        public function login(Request $request)
        {
            $email = $request->email;
            $password = $request->password;

            $user = User::where('email', $email)->first();

            if (!$user) {
                return response()->json([
                    'status' => 'Error',
                    'message' => 'user not exist',
                ],404);
            }

            if (!Hash::check($password, $user->password)) {
                return response()->json([
                    'status' => 'Error',
                    'message' => 'wrong password',
                ],400);
            }

            $user->token = Str::random(36); //
            $user->save(); //

            return response()->json([
                'status' => 'Success',
                'message' => 'successfully login',
                'data' => [
                    'user' => $user,
                ]
            ],200);
        }
    }
    ```

    <p align="center">
       <img src="pic/ss3-04.png" width=450></img><br>
       <i>Gambar 3.4: Kode untuk menambahkan token pada user saat melakukan login</i>
    </p>

5. Jalankan perintah di bawah untuk menjalankan migrasi terbaru

    ```
    php artisan migrate
    ```

    <p align="center">
       <img src="pic/ss3-05.png" width=500></img><br>
       <i>Gambar 3.5: Melakukan migrate database</i>
    </p>

6. Jalankan aplikasi pada endpoint /auth/login dengan body berikut. Salinlah token yang didapat ke notepad

    ```JSON
    {
        "email": "scaramouche@fatui.org",
        "password": "wanderer"
    }
    ```

    <p align="center">
       <img src="pic/ss3-06.png" width=500></img><br>
       <i>Gambar 3.6: Melakukan login dan mendapatkan token</i>
    </p>