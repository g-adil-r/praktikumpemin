# **Bab 9: JSON Web Token (JWT)**
**Oleh Ghifari Adil Ruchiyat - 215150701111003**

## Penyesuaian Database
1. Lakukan perubahan pada length kolom token dengan menghapus parameter 72 di belakangnya

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
        // from this
        public function up()
        {
            Schema::table('users', function (Blueprint $table) {
            $table->string('token', 72)->unique()->nullable(); //
            });
        }

        // to this
        public function up()
        {
            Schema::table('users', function (Blueprint $table) {
            $table->string('token')->unique()->nullable();
            });
        }
        
        ...
    }
    ```

    <p align="center">
       <img src="pic/ss1-01.png" width=350></img><br>
       <i>Gambar 1.1: Menyesuaikan database agar dapat menggunakan JWT</i>
    </p>

2. Jalankan perintah di bawah untuk memperbaharui migrasi dan menghapus data yang lama

    ```
    php artisan migrate:fresh
    ```

    <p align="center">
       <img src="pic/ss1-02.png" width=450></img><br>
       <i>Gambar 1.2: Memperbaharui migrasi database</i>
    </p>

3. Jalankan aplikasi pada endpoint `/auth/register` dengan body berikut.

    ```JSON
    {
        "name": "Scaramouche",
        "email": "scaramouche@fatui.org",
        "password": "wanderer"
    }
    ```

    <p align="center">
       <img src="pic/ss1-03.png" width=450></img><br>
       <i>Gambar 1.3: Melakukan registrasi user baru</i>
    </p>

---
## JWT Manual
1. Tambahkan ketiga fungsi berikut pada AuthController.php

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

        private function base64url_encode(String $data): String
        {
            $base64 = base64_encode($data); // ubah json string menjadi base64
            $base64url = strtr($base64, '+/', '-_'); // ubah char '+' -> '-' dan '/' -> '_'

            return rtrim($base64url, '='); // menghilangkan '=' pada akhir string
        }

        private function sign(String $header, String $payload, String $secret): String
        {
            $signature = hash_hmac('sha256', "{$header}.{$payload}", $secret, true);
            $signature_base64url = $this->base64url_encode($signature);

            return $signature_base64url;
        }

        private function jwt(array $header, array $payload, String $secret): String
        {
            $header_json = json_encode($header);
            $payload_json = json_encode($payload);

            $header_base64url = $this->base64url_encode($header_json);
            $payload_base64url = $this->base64url_encode($payload_json);
            $signature_base64url = $this->sign($header_base64url, $payload_base64url, $secret);

            $jwt = "{$header_base64url}.{$payload_base64url}.{$signature_base64url}";
            
            return $jwt;
        }
    }
    ```

    <p align="center">
       <img src="pic/ss2-01.png" width=500></img><br>
       <i>Gambar 2.1: Menambahkan fungsi baru pada controller untuk membuat token</i>
    </p>

2. Lakukan perubahan pada fungsi login

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

            //
            $jwt = $this->jwt(
                [
                    'alg' => 'HS256',
                    'typ' => 'JWT'
                ],
                [
                    'id' => $user->id,
                ],
                'secret'
            );

            $user->token = $jwt;
            //
            $user->save();

            return response()->json([
                'status' => 'Success',
                'message' => 'successfully login',
                'data' => [
                    'user' => $user,
                ]
            ],200);
        }

        ...
    }
    ```

    <p align="center">
       <img src="pic/ss2-02.png" width=300></img><br>
       <i>Gambar 2.2: Mengubah fungsi login pada AuthController agar menggunakan JWT</i>
    </p>

3. Tambahkan keempat fungsi berikut pada Middleware/Authorization.php

    ```php
    <?php

    namespace App\Http\Middleware;

    use App\Models\User;
    use Closure;

    class Authorization
    {
        ...

        private function base64url_encode(string $data): string
        {
            $base64 = base64_encode($data);
            $base64url = strtr($base64, '+/', '-_');

            return rtrim($base64url, '=');
        }

        private function base64url_decode(string $base64url): string
        {
            $base64 = strtr($base64url, '-_', '+/');
            $json = base64_decode($base64);

            return $json;
        }

        private function sign(string $header_base64url, string $payload_base64url, string $secret): string
        {
            $signature = hash_hmac('sha256', "{$header_base64url}.{$payload_base64url}", $secret, true);
            $signature_base64url = $this->base64url_encode($signature);

            return $signature_base64url;
        }

        private function verify(string $signature_base64url, string $header_base64url, string $payload_base64url, string $secret): bool
        {
            $signature = $this->base64url_decode($signature_base64url);
            $expected_signature = $this->base64url_decode($this->sign($header_base64url, $payload_base64url, $secret));
            
            return hash_equals($expected_signature, $signature);
        }
    }
    ```

    <p align="center">
       <img src="pic/ss2-03.png" width=500></img><br>
       <i>Gambar 2.3: Menambahkan fungsi pada middleware Authorization agar menggunakan JWT</i>
    </p>

4. Lakukan perubahan pada fungsi handle

    ```php
    <?php

    namespace App\Http\Middleware;

    use App\Models\User;
    use Closure;

    class Authorization
    {
        /**
         * Handle an incoming request.
         *
         * @param \Illuminate\Http\Request $request
         * @param \Closure $next
         * @return mixed
         */
        public function handle($request, Closure $next)
        {
            $token = $request->header('token') ?? $request->query('token');
            if (!$token) {
                return response()->json([
                    'status' => 'Error',
                    'message' => 'token not provided',
                ],400);
            }

            //
            [
                $header_base64url,
                $payload_base64url,
                $signature_base64url
            ] = explode('.', $token);

            $header = $this->base64url_decode($header_base64url);
            $json_header = json_decode($header);

            if (!$json_header->alg || $json_header->alg !== 'HS256') {
                return response()->json([
                    'status' => 'Error',
                    'message' => 'type of token not valid',
                ],401);
            }

            if (!$json_header->typ || $json_header->typ !== 'JWT') {
                return response()->json([
                    'status' => 'Error',
                    'message' => 'type of token not valid',
                ],401);
            }

            $payload = $this->base64url_decode($payload_base64url);
            $json_payload = json_decode($payload);
            if (!$json_payload->id) {
                return response()->json([
                    'status' => 'Error',
                    'message' => 'invalid token',
                ],400);
            }

            $verified = $this->verify($signature_base64url, $header_base64url, $payload_base64url, 'secret');
                if (!$verified) {
                return response()->json([
                    'status' => 'Error',
                    'message' => 'invalid sign token',
                ],400);
            }
            $id = $json_payload->id;
            $user = User::where('id', $id)->first();
            //
            if (!$user) {
                return response()->json([
                    'status' => 'Error',
                    'message' => 'invalid token',
                ],400);
            }
            $request->user = $user;
            return $next($request);
        }
        ...
    }
    ```

    <p align="center">
       <img src="pic/ss2-04.png" width=400></img><br>
       <i>Gambar 2.4: Mengubah fungsi handle pada middleware Authorization agar menggunakan JWT</i>
    </p>

5. Jalankan aplikasi pada endpoint `/auth/login` dengan body berikut. Salinlah token yang didapat ke notepad

    ```json
    {
        "email": "scaramouche@fatui.org",
        "password": "wanderer"
    }
    ```

    <p align="center">
       <img src="pic/ss2-05.png" width=600></img><br>
       <i>Gambar 2.5: Melakukan login dan mendapatkan token JWT</i>
    </p>

6. Jalankan aplikasi pada endpoint /home dengan melampirkan nilai token yang didapat setelah login pada header

    <p align="center">
       <img src="pic/ss2-06.png" width=600></img><br>
       <i>Gambar 2.6: Masuk ke endpoint home dengan menggunakan token JWT</i>
    </p>

---

## JWT Library
1. Lakukan generate jwt key secara online menggunakan website Djecrety ― Django Secret Key Generator

    <p align="center">
       <img src="pic/ss3-01-1.png" width=500></img><br>
       <i>Gambar 3.1: Membuat secret key menggunakan website Djecrety</i>
    </p>

    Setelah mendapatkan secret key kita akan memasukkan secret key tersebut pada file .env dengan membuat variable baru bernama `JWT_SECRET`

    <p align="center">
       <img src="pic/ss3-01-2.png" width=400></img><br>
       <i>Gambar 3.2: Menambahkan secret key ke variable <code>JWT_SECRET</code> pada file .env</i>
    </p>

2. Lakukan instalasi package jwt firebase dengan menggunakan command berikut

    ```
    composer require firebase/php-jwt
    ```

    <p align="center">
       <img src="pic/ss3-02.png" width=500></img><br>
       <i>Gambar 3.3: Menginstall package JWT Firebase</i>
    </p>

3. Tambahkan fungsi berikut pada file AuthController

    ```php
    <?php

    namespace App\Http\Controllers;

    use App\Models\User;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Hash;
    use Illuminate\Support\Str;

    //
    use Firebase\JWT\JWT;

    class AuthController extends Controller
    {
        /**
         * Create a new controller instance.
         *
         * @return void
         */
        public function __construct(Request $request) //
        {
            //
            $this->request = $request;
        }
        //
        protected function jwt(User $user)
        {
            $payload = [
                'iss' => 'lumen-jwt', //issuer of the token
                'sub' => $user->id, //subject of the token
                'iat' => time(), //time when JWT was issued.
                'exp' => time() + 60 * 60 //time when JWT will expire
            ];
            return JWT::encode($payload, env('JWT_SECRET'), 'HS256');
        }

        ...
    }
    ```

    <p align="center">
       <img src="pic/ss3-03.png" width=450></img><br>
       <i>Gambar 3.4: Membuat fungsi JWT pada AuthController menggunakan library JWT Firebase</i>
    </p>

4. Lakukan perubahan pada fungsi login menjadi seperti berikut

    ```php
    <?php

    namespace App\Http\Controllers;

    use App\Models\User;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Hash;
    use Illuminate\Support\Str;
    //
    use Firebase\JWT\JWT;

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
                ], 404);
            }

            if (!Hash::check($password, $user->password)) {
                return response()->json([
                    'status' => 'Error',
                    'message' => 'wrong password',
                ], 400);
            }

            $user->token = $this->jwt($user); //
            $user->save();

            return response()->json([
                'status' => 'Success',
                'message' => 'successfully login',
                'data' => [
                    'user' => $user,
                ]
            ], 200);
        }
    }
    ```

    <p align="center">
       <img src="pic/ss3-04.png" width=450></img><br>
       <i>Gambar 3.5: Mengubah fungsi login agar menggunakan Library JWT Firebase</i>
    </p>

5. Buatlah file JwtMiddleware.php dan isikan baris code berikut

    ```php
    <?php

    namespace App\Http\Middleware;

    use Closure;
    use Exception;
    use App\Models\User;
    use Firebase\JWT\JWT;
    use Firebase\JWT\Key;
    use Firebase\JWT\ExpiredException;

    class JwtMiddleware
    {
        function handle($request, Closure $next, $guard = null)
        {
            $token = $request->header('token') ?? $request->query('token');
            // $token = $request->get('token');

            if (!$token) {
                //Unauthorized response if token not there
                return response()->json([
                    'error' => 'Token not provded.'
                ], 401);
            }

            try {
                $credentials = JWT::decode($token, new Key(env('JWT_SECRET'), 'HS256'));
            } catch (ExpiredException $e) {
                return response()->json([
                    'error' => 'Provided token is expired.'
                ], 400);
            } catch (Exception $e) {
                return response()->json([
                    'error' => 'An error while decoding token.'
                ], 400);
            }

            $user = User::find($credentials->sub);
            
            $request->user = $user;
            return $next($request);
        }
    }
    ```

    <p align="center">
       <img src="pic/ss3-05.png" width=450></img><br>
       <i>Gambar 3.6: Membuat middleware baru bernama JWTMiddleware</i>
    </p>

6. Daftarkan middleware yang telah dibuat pada `bootstrap/app.php`

    ```php
    $app->routeMiddleware([
        'jwt.auth' => App\Http\Middleware\JwtMiddleware::class,
    ]);
    ```

    <p align="center">
       <img src="pic/ss3-06.png" width=450></img><br>
       <i>Gambar 3.7: Mendaftarkan JWTMiddleware ke <code>bootstrap/app.php</code> </i>
    </p>

7. Tambahkan baris berikut pada file web.php

    ```php
    $router->get('/home', ['middleware' => 'jwt.auth', 'uses' => 'HomeController@home']);
    ```

    <p align="center">
       <img src="pic/ss3-07.png" width=450></img><br>
       <i>Gambar 3.8: Menambah endpoint /home yang menggunakan JWTMiddleware </i>
    </p>

8. Jalankan aplikasi pada endpoint /auth/login dengan body berikut. Salinlah token yang didapat ke notepad

    ```json
    {
        "email": "scaramouche@fatui.org",
        "password": "wanderer"
    }
    ```

    <p align="center">
       <img src="pic/ss3-08.png" width=600></img><br>
       <i>Gambar 3.9: Melakukan login dan mendapatkan token JWT baru</i>
    </p>

9. Jalankan aplikasi pada endpoint /home dengan melampirkan nilai token yang didapat setelah login pada header

    <p align="center">
       <img src="pic/ss3-09.png" width=600></img><br>
       <i>Gambar 3.10: Masuk ke endpoint home dengan menggunakan token JWT baru</i>
    </p>
