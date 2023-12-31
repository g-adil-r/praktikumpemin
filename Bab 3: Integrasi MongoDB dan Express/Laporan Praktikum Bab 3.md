# **BAB 3: Integrasi MongoDB dan Express**
**Oleh Ghifari Adil Ruchiyat - 215150701111003**

## Percobaan instalasi NodeJS
1. Buka halaman https://nodejs.org/en

2. Download dan jalankan node setup

3. Setelah instalasi selesai jalankan command `node -v` untuk memeriksa apakah NodeJS sudah terinstall
   <p align="center">
      <img src="pic/ss1-01.png" width=400></img><br>
      <i>Gambar 1.1: Hasil command <code>node -v</code> </i>
   </p>

---------------
## Inisiasi project Express dan pemasangan package
1. Lakukan pembuatan folder dengan nama express-mongodb dan masuk ke dalam folder tersebut lalu buka melalui text editor masing-masing
   <p align="center">
      <img src="pic/ss2-01.png" width=400></img><br>
      <i>Gambar 2.1: Membuat folder express-mongodb</i>
   </p>

2. Lakukan npm init untuk mengenerate file package.json dengan menggunakan command `npm init -y`
   <p align="center">
      <img src="pic/ss2-02.png" width=500></img><br>
      <i>Gambar 2.2: Menjalankan command <code>npm init -y</code> </i>
   </p>

3. Lakukan instalasi express, mongoose, dan dotenv dengan menggunakan command `npm i express mongoose dotenv`
   <p align="center">
      <img src="pic/ss2-03.png" width=500></img><br>
      <i>Gambar 2.3: Menginstall express, mongoose, dan dotenv</i>
   </p>

## Koneksi Express ke MongoDB
1. Buatlah file index.js pada root folder dan masukkan kode di bawah ini
   
   ```javascript
   require('dotenv').config();
   const express = require('express');
   const mongoose = require('mongoose');

   const app = express();

   app.use(express.json());
   app.get('/', (req, res) => {
      res.status(200).json({
         message: '<nama>,<nim>'
      })
   })

   const PORT = 8000;
   app.listen(PORT, () => {
      console.log(`Running on port ${PORT}`);
   })
   ```
   
   Setelah itu coba jalankan aplikasi dengan command `node index.js`
   <p align="center">
      <img src="pic/ss3-01.png" width=500></img><br>
      <i>Gambar 3.1: Menambahkan kode ke index.js, lalu menjalankannya</i>
   </p>

2. Lakukan pembuatan file .env dan masukkan baris berikut

       PORT=5000
   
   <p align="center">
      <img src="pic/ss3-02-1.png" width=250></img><br>
      <i>Gambar 3.2: Menambahkan file .env dan menambahkan nomor port</i>
   </p>

   Setelah itu ubahlah kode pada listening port menjadi berikut dan coba jalankan aplikasi kembali

   ```javascript
   ...
   const PORT = process.env.PORT || 8000;
   app.listen(PORT, () => {
      console.log(`Running on port ${PORT}`);
   })
   ```

   <p align="center">
      <img src="pic/ss3-02-2.png" width=500></img><br>
      <i>Gambar 3.3: Menggunakan file .env untuk nomor port</i>
   </p>

3. Copy connection string yang terdapat pada compas atau atlas dan paste kan pada .env seperti berikut

       MONGO_URI=<Connection string masing-masing>

   <p align="center">
      <img src="pic/ss3-03.png" width=400></img><br>
      <i>Gambar 3.4: Menambahkan connection string pada file .env</i>
   </p>

4. Tambahkan baris kode berikut pada file index.js

   ```javascript
   require('dotenv').config();
   const express = require('express');
   const mongoose = require('mongoose');
   
   mongoose.connect(process.env.MONGO_URI);
   const db = mongoose.connection;
   
   db.on('error', (error) => {
      console.log(error);
   });
   
   db.once('connected', () => {
      console.log('Mongo connected');
   })

   ...
   ```
   
   Setelah itu coba jalankan aplikasi kembali

   <p align="center">
      <img src="pic/ss3-04.png" width=500></img><br>
      <i>Gambar 3.5: Menambahkan kode untuk koneksi ke database mongoDB</i>
   </p>

## Pembuatan routing
1. Lakukan pembuatan direktori routes di tingkat yang sama dengan index.js

   <p align="center">
      <img src="pic/ss4-01.png" width=250></img><br>
      <i>Gambar 4.1: Menambahkan folder routes</i>
   </p>

2. Buatlah file book.route.js di dalamnya

   <p align="center">
      <img src="pic/ss4-02.png" width=250></img><br>
      <i>Gambar 4.2: Menambahkan file book.route.js ke dalam folder route</i>
   </p>

3. Tambahkan baris kode berikut untuk fungsi getAllBooks

   ```javascript
   const router = require('express').Router();
   
   router.get('/', function getAllBooks(req, res) {
      res.status(200).json({
         message: 'mendapatkan semua buku'
      })
   })
   
   module.exports = router;
   ```

   <p align="center">
      <img src="pic/ss4-03.png" width=500></img><br>
      <i>Gambar 4.3: Menambahkan function getAllBooks pada file route</i>
   </p>

4. Lakukan hal yang sama untuk getOneBook, createBook, updateBook, dan deleteBook

   ```javascript
   const router = require('express').Router();

   ...

   router.get('/:id', function getOneBook(req, res) {
      const id = req.params.id;
      res.status(200).json({
         message: 'mendapatkan satu buku',
         id,
      })
   })

   router.post('/', function createBook(req, res) {
      res.status(200).json({
         message: 'membuat buku baru'
      })
   })

   router.put('/:id', function updateBook(req, res) {
      const id = req.params.id;
      res.status(200).json({
         message: 'memperbaharui satu buku',
         id,
      })
   })

   router.delete('/:id', function deleteBook(req, res) {
      const id = req.params.id;
      res.status(200).json({
         message: 'menghapus satu buku',
         id,
      })
   })

   module.exports = router;
   ```

   <p align="center">
      <img src="pic/ss4-04.png" width=500></img><br>
      <i>Gambar 4.4: Menambahkan function getOneBook, createBook, updateBook, dan deleteBook pada route</i>
   </p>

5. Lakukan import book.route.js pada file index.js dan tambahkan baris kode berikut

   ```javascript
   require('dotenv').config();

   const express = require('express');
   const mongoose = require('mongoose');
   const bookRoutes = require('./routes/book.route');

   ...

   app.get('/', (req, res) => {
      res.status(200).json({
         message: '<nama>,<nim>'
      })
   })

   app.use('/books', bookRoutes);

   const PORT = process.env.PORT || 8000;
   app.listen(PORT, () => {
      console.log(`Running on port ${PORT}`);
   })
   ```

   <p align="center">
      <img src="pic/ss4-05.png" width=500></img><br>
      <i>Gambar 4.5: Menambahkan kode pada file index.js</i>
   </p>

6. Uji salah satu endpoint dengan Postman
   
   <p align="center">
      <img src="pic/ss4-06.png" width=500></img><br>
      <i>Gambar 4.6: Menguji endpoint dengan Postman</i>
   </p>

## Pembuatan controller
1. Lakukan pembuatan direktori controllers di tingkat yang sama dengan index.js

   <p align="center">
      <img src="pic/ss5-01.png" width=250></img><br>
      <i>Gambar 5.1: Membuat folder controllers</i>
   </p>

2. Buatlah file book.controller.js di dalamnya

   <p align="center">
      <img src="pic/ss5-02.png" width=250></img><br>
      <i>Gambar 5.2: Membuat file books.controller.js di dalam folder controllers</i>
   </p>

3. Salin baris kode dari routes untuk fungsi getAllBooks

   ```javascript
   function getAllBooks(req, res) {
      res.status(200).json({
         message: 'mendapatkan semua buku'
      })
   };

   module.exports = {
      getAllBooks,
   }
   ```

   <p align="center">
      <img src="pic/ss5-03.png" width=400></img><br>
      <i>Gambar 5.3: Menambahkan function getAllBooks pada controller</i>
   </p>

4. Lakukan hal yang sama untuk getOneBook, createBook, updateBook, dan deleteBook
   ```javascript
   ...

   function getOneBook(req, res) {
      const id = req.params.id;
      res.status(200).json({
         message: 'mendapatkan satu buku',
         id,
      })
   }

   function createBook(req, res) {
      res.status(200).json({
         message: 'membuat buku baru'
      })
   }

   function updateBook(req, res) {
      const id = req.params.id;
      res.status(200).json({
         message: 'memperbaharui satu buku',
         id,
      })
   }

   function deleteBook(req, res) {
      const id = req.params.id;
      res.status(200).json({
         message: 'menghapus satu buku',
         id,
      })
   }

   module.exports = {
      getAllBooks,
      getOneBook, 
      createBook, 
      updateBook, 
      deleteBook 
   }
   ```

   <p align="center">
      <img src="pic/ss5-04.png" width=500></img><br>
      <i>Gambar 5.4: Menambahkan function getOneBook, createBook, updateBook, dan deleteBook pada controller</i>
   </p>

5. Lakukan import book.controller.js pada file book.route.js
   ```javascript
   const router = require('express').Router();
   const book = require('../controllers/book.controller');

   ...

   module.exports = router;
   ```

   <p align="center">
      <img src="pic/ss5-05.png" width=500></img><br>
      <i>Gambar 5.5: Mengimport controller ke file route</i>
   </p>

6. Lakukan perubahan pada fungsi agar dapat memanggil fungsi dari book.controller.js
   ```javascript
   const router = require('express').Router();
   const book = require('../controllers/book.controller');

   router.get('/', book.getAllBooks);
   router.get('/:id', book.getOneBook);
   router.post('/', book.createBook);
   router.put('/:id', book.updateBook);
   router.delete('/:id', book.deleteBook);

   module.exports = router;
   ```

   <p align="center">
      <img src="pic/ss5-06.png" width=500></img><br>
      <i>Gambar 5.6: Menggunakan controller sebagai callback function</i>
   </p>

7. Lakukan pengujian kembali, pastikan response tetap sama

   <p align="center">
      <img src="pic/ss5-07.png" width=500></img><br>
      <i>Gambar 5.7: Menguji ulang endpoint dengan Postman</i>
   </p>

## Pembuatan model
1. Lakukan pembuatan direktori models di tingkat yang sama dengan index.js

   <p align="center">
      <img src="pic/ss6-01.png" width=250></img><br>
      <i>Gambar 6.1: Membuat folder models</i>
   </p>

2. Buatlah file book.model.js di dalamnya

   <p align="center">
      <img src="pic/ss6-02.png" width=250></img><br>
      <i>Gambar 6.2: Membuat file book.model.js di dalam folder models</i>
   </p>

3. Tambahkan baris kode berikut sesuai dengan tabel di atas
   ```javascript
   const mongoose = require('mongoose');

   const bookSchema = new mongoose.Schema({
      title: {
         type: String
      },
      author: {
         type: String
      },
      year: {
         type: Number
      },
      pages: {
         type: Number
      },
      summary: {
         type: String
      },
      publisher: {
         type: String
      }
   })

   module.exports = mongoose.model('book', bookSchema);
   ```

   <p align="center">
      <img src="pic/ss6-03.png" width=400></img><br>
      <i>Gambar 6.3: Membuat model untuk book pada file book.model.js</i>
   </p>

## Operasi CRUD
1. Hapus semua data pada collection books

   <p align="center">
      <img src="pic/ss7-01.png" width=600></img><br>
      <i>Gambar 7.1: Menghapus data pada collection books</i>
   </p>

2. Lakukan import book.model.js pada file book.controller.js

   ```javascript
   const Book = require('../models/book.model');
   ```

   <p align="center">
      <img src="pic/ss7-02.png" width=400></img><br>
      <i>Gambar 7.2: Mengimport model pada controller</i>
   </p>

3. Lakukan perubahan pada fungsi createBook
   ```javascript
   async function createBook(req, res) {
      const book = new Book({
         title: req.body.title,
         author: req.body.author,
         year: req.body.year,
         pages: req.body.pages,
         summary: req.body.summary,
         publisher: req.body.publisher,
      })
      try {
         const savedBook = await book.save();
         res.status(200).json({
            message: 'membuat buku baru',
            book: savedBook,
         })
      } catch (error) {
         res.status(500).json({
            message: 'kesalahan pada server',
            error: error.message,
         })
      }
   }
   ...
   ```

   <p align="center">
      <img src="pic/ss7-03.png" width=500></img><br>
      <i>Gambar 7.3: Mengubah kode pada controller untuk menambahkan data buku baru ke mongoDB</i>
   </p>

4. Buatlah dua buah buku dengan data di bawah ini dengan Postman
   ```JSON
   {
      "title": "Dilan 1990",
      "author": "Pidi Baiq",
      "year": 2014,
      "pages": 332,
      "summary": "Mirea, anata wa utsukushī",
      "publisher": "Pastel Books"
   }
   ```
   ```JSON
   {
      "title": "Dilan 1991",
      "author": "Pidi Baiq",
      "year": 2015,
      "pages": 344,
      "summary": "Watashi ga kare o aishite iru to ittara",
      "publisher": "Pastel Books"
   }
   ```

   <p align="center">
      <img src="pic/ss7-04-1.png" width=500></img><br>
      <i>Gambar 7.4: Menambahkan data buku pertama dengan Postman</i>
   </p>

   <p align="center">
      <img src="pic/ss7-04-2.png" width=500></img><br>
      <i>Gambar 7.5: Menambahkan data buku kedua dengan Postman</i>
   </p>

   <p align="center">
      <img src="pic/ss7-04-3.png" width=500></img><br>
      <i>Gambar 7.6: Hasil penambahan data pada mongoDB</i>
   </p>

5. Lakukan perubahan pada fungsi getAllBooks
   ```javascript
   async function getAllBooks(req, res) {
      try {
         const books = await Book.find();
         res.status(200).json({
            message: 'mendapatkan semua buku',
            books,
         })
      } catch (error) {
         res.status(500).json({
            message: 'kesalahan pada server',
            error: error.message,
         })
      }
   }
   ...
   ```

   <p align="center">
      <img src="pic/ss7-05.png" width=500></img><br>
      <i>Gambar 7.7: Mengubah kode untuk melihat semua data buku</i>
   </p>

6. Lakukan perubahan pada fungsi getOneBook
   ```javascript
   async function getOneBook(req, res) {
      const id = req.params.id;
      try {
         const book = await Book.findById(id);
         res.status(200).json({
            message: 'mendapatkan satu buku',
            book,
         })
      } catch (error) {
         res.status(500).json({
            message: 'kesalahan pada server',
            error: error.message,
         })
      }
   }
   ...
   ```

   <p align="center">
      <img src="pic/ss7-06.png" width=500></img><br>
      <i>Gambar 7.8: Mengubah kode untuk melihat data buku berdasarkan IDnya</i>
   </p>

7. Tampilkan semua buku dengan Postman

   <p align="center">
      <img src="pic/ss7-07.png" width=500></img><br>
      <i>Gambar 7.9: Mendapatkan data semua buku lewat Postman</i>
   </p>

8. Tampilkan buku Dilan 1990 dengan Postman

   <p align="center">
      <img src="pic/ss7-08.png" width=500></img><br>
      <i>Gambar 7.10: Mendapatkan data buku Dillan 1990 (id <code>6511420120be8db40efdc743</code>) dengan Postman</i>
   </p>

9. Lakukan perubahan pada fungsi updateBook
   ```javascript
   async function updateBook(req, res) {
      const id = req.params.id;
      try {
         const book = await Book.findByIdAndUpdate(
            id, req.body, { new: true }
         )
         res.status(200).json({
            message: 'memperbaharui satu buku',
            book,
         })
      } catch (error) {
         res.status(500).json({
            message: 'kesalahan pada server',
            error: error.message,
         })
      }
   }
   ...
   ```

   <p align="center">
      <img src="pic/ss7-09.png" width=500></img><br>
      <i>Gambar 7.11: Mengubah kode untuk mengupdate buku berdasarkan IDnya</i>
   </p>

10. Ubah judul buku Dilan 1991 menjadi "\<NAMA PANGGILAN\> 1991" dengan Postman

   <p align="center">
      <img src="pic/ss7-10.png" width=500></img><br>
      <i>Gambar 7.12: Mengubah judul buku pada buku Dilan 1991 (id <code>6511424020be8db40efdc745</code>)</i>
   </p>

11. Lakukan perubahan pada fungsi deleteBook
   ```javascript
   async function deleteBook(req, res) {
      const id = req.params.id;
      try {
         const book = await Book.findByIdAndDelete(id);
         res.status(200).json({
            message: 'menghapus satu buku',
            book,
         })
      } catch (error) {
         res.status(500).json({
            message: 'kesalahan pada server',
            error: error.message,
         })
      }
   }
   ...
   ```

   <p align="center">
      <img src="pic/ss7-11.png" width=500></img><br>
      <i>Gambar 7.13: Mengubah kode untuk emnghapus buku berdasarkan IDnya</i>
   </p>

12. Hapus buku Dilan 1990 dengan Postman

   <p align="center">
      <img src="pic/ss7-12.png" width=500></img><br>
      <i>Gambar 7.14: Menghapus buku Dilan 1990 (id <code>6511420120be8db40efdc743</code>) dengan Postman</i>
   </p>