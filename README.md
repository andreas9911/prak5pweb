# Praktikum Pemrograman Web 5
Andreas Rona Nugraha

# Penjelasan Umum
Web yang saya buat adalah web seperti wikipedia dari karakter-karakter game Genshin Impact. Berisikan biodata singkat, talenta, dan anjuran artefak yang dapat dibuat menggunakan Form Tambah Karakter di bagian Dashboard (harus login). Jika tidak login, maka hanya dapat melihat-lihat informasi yang ada, namun jika login maka selain lihat - lihat informasi dapat juga membuka menu dashboard yang dapat menambahkan karakter, edit, cari, dan juga hapus data-data karakter.

# Cara menjalankan (di local)
1. Install Composer
2. Install XAMPP
3. Jalankan Apache dan MySQL di XAMPP
4. Buka CMD
5. Di CMD ke direktori xampp/mysql/bin
6. Lalu mysql -u root -p
7. Untuk password di enter aja
8. Lalu masukkan query : create database uts
9. Lalu buka projek yang berada di branch master, klik tombol code, dan download zip
10. Extract zip tersebut ke direktori C:/xampp/htdocs/(buat folder project)
11. Buka project melalui open folder di vscode
12. Lalu ke direktori database/migrations
13. Buka 2014_10_12_000000_create_users_table.php lalu buka terminal vscode dan jalankan php artisan migrate
14. Sama dengan langkah 13 namun di 2022_10_23_033404_create_karakter_table.php
15. Setelah itu masukkan manual melalui phpmyadmin untuk data users, nanti akan digunakan untuk login untuk membuka dashboard (CRUD hanya bisa diakses setelah login)
16. Lalu di terminal vscode, jalankan php artisan serve
17. Buka link yang muncul disitu untuk menjalankan web.
18. Klik login untuk membuka menu login, isi form sesuai dengan data di tabel users, dan akan terbuka menu dashboard.
19. Di situ dapat melakukan pencarian berdasarkan id_karakter, penambahan karakter, edit karakter, dan hapus karakter.
20. Untuk fitur log out belum ada, jadi bisa dengan cara restart xampp, browser, dan php artisan serve.

# Desain Database
Ada 2 tabel
Pertama tabel users yang mempunyai kolom 
id (auto increment), 
name, 
email, 
email_verified_at, 
password, 
remember_token, 
created_at, 
updated_at. 
Tabel Users ini digunakan untuk login agar dapat membuka menu dashboard.

Kedua tabel datakarakter yang mempunyai kolom 
id_karakter (auto increment), 
nama_karakter, 
alias, 
vision, 
asal, 
tipe_senjata, 
tipe_model, 
tanggal_rilis, 
created_at, 
updated_at, 
nama_talenta_normal, 
deskripsi_talenta_normal, 
nama_talenta_skill, 
deskripsi_talenta_skill, 
nama_talenta_burst, 
deskripsi_talenta_burst, 
anjuran_normal, 
anjuran_skill, 
anjuran_burst, 
set_artefak, 
anjuran_artefak, 
lore, 
deskripsi_karakter. 
Tabel ini digunakan untuk informasi-informasi karakter yang akan ditampilkan.

# Routes
Ada 13 routes dengan 4 method HTTP:
1. Route::get('/',[PageController::class,'home']);
Pada route ini akan menjalankan function home yang ada di PageController, url yang digunakan 127.0.0.1:8000. Ini adalah menu utama (home), yang akan menampilkan karakter-karakter yang ada lalu ada tombol login dibagian navbar.

2. Route::get('/karakter={id_karakter}',[PageController::class,'karakter']);
Pada route ini akan menjalankan function karakter yang ada di PageController, url yang digunakan 127.0.0.1:8000/karakter={id_karakter}, jadi jika karakter Kamisato Ayaka mempunyai id_karakter = 1, maka pada url 127.0.0.1:8000/karakter=1 akan menampilkan detail iformasi dari karakter Kamisato Ayaka. Menu ini adalah menu detail informasi dari karakter.

3. Route::get('/login',[AuthController::class,'index'])->name('login');
Pada route ini akan menjalankan function index pada AuthController, url yang digunakan 127.0.0.1:8000/login. Ini adalah menu login, dibutuhkan Email dan Password yang sudah dimasukkan secara manual pada database tabel users.

4. Route::post('/login',[AuthController::class,'login']);
Pada route ini akan menjalankan function login pada AuthController, route ini untuk melakukan POST form login, jika benar akan lanjut ke dashboard (route nomor 5), jika tidak benar akan kembali ke menu login (route nomor 3).

5. Route::get('/dashboard',[DashboardController::class, 'index'])->middleware('auth');
Pada route ini akan menjalankan function index pada DashboardController. Route ini hanya dapat diakses setelah kita login, jika belum login dan memasukkan url ini maka akan redirect otomatis ke menu login (route nomor 3). Di menu Dashboard kita dapat melihat data - data karakter yang telah di masukkan dan juga mencari data karakter berdasarkan nama_karakter pada tabel datakarakter.

6. Route::get('/dashboard',[DashboardController::class,'search'])->middleware('auth');
Pada route ini akan menjalankan function search pada DashboardController. Route ini hanya dapat diakses setelah kita login, jika belum login dan memasukkan url ini maka akan redirect otomatis ke menu login (route nomor 3). Route ini untuk melakukan pencarian data karakter berdasarkan nama_karakter pada tabel datakarakter, tampilannya sama dengan routet nomor 5 namun data yang tampil sesuai dengan pencarian.

7. Route::get('/dashboard/karakter',[KarakterController::class,'index'])->middleware('auth');
Pada route ini akan menjalankan function index pada KarakterController. Route ini hanya dapat diakses setelah kita login, jika belum login dan memasukkan url ini maka akan redirect otomatis ke menu login (route nomor 3). Di sini kita dapat melihat data-data karakter yang sudah dimasukkan namun dengan informasi yang ditampilkan lebih sedikit dari pada di menu dashboard (route nomor 5). Di sini kita dapat melakukan pencarian (berdasarkan nama_karakter), tambah karakter, edit karakter, dan hapus karakter.

8. Route::get('/dashboard/karakter/tambah',[KarakterController::class,'create'])->middleware('auth');
Pada route ini akan menjalankan function create pada KarakterController. Route ini hanya dapat diakses setelah kita login, jika belum login dan memasukkan url ini maka akan redirect otomatis ke menu login (route nomor 3). Ini merupakan menu tambah karakter, berisikan form untuk pengisian informasi - informasi berkaitan dengan karakter.

9. Route::post('/dashboard/karakter/store',[KarakterController::class,'store'])->middleware('auth');
Pada route ini akan menjalankan function store pada KarakterController. Route ini hanya dapat diakses setelah kita login, jika belum login dan memasukkan url ini maka akan redirect otomatis ke menu login (route nomor 3). Route ini berjalan setelah klik tombol "Tambah" di route nomor 8. Route ini tidak mempunyai tampilan, route ini hanya berguna untuk memasukkan data-data ke database tabel datakarakter. Akan redirect ke route nomor 7.

10. Route::get('/dashboard/karakter/edit={id_karakter}',[KarakterController::class,'edit'])->middleware('auth');
Pada route ini akan menjalankan function edit pada KarakterController. Route ini hanya dapat diakses setelah kita login, jika belum login dan memasukkan url ini maka akan redirect otomatis ke menu login (route nomor 3). Ini merupakan menu edit karakter, berisikan form yang sama seperti tambah karakter, namun sudah ada isinya, ganti isi form dan klik tombol "Selesai" untuk melakukan edit data karakter.

11. Route::put('/dashboard/karakter/{id_karakter}',[KarakterController::class,'update'])->middleware('auth');
Pada route ini akan menjalankan function update pada KarakterController. Route ini hanya dapat diakses setelah kita login, jika belum login dan memasukkan url ini maka akan redirect otomatis ke menu login (route nomor 3). Route ini berjalan setelah klik tombol "Selesai" di route nomor 10. Route ini tidak mempunyai tampilan, route ini hanya berguna untuk mengupdate data-data ke database tabel datakarakter sesuai dengan karakter yang diedit. Akan redirect ke route nomor 7.

12. Route::delete('/dashboard/karakter/hapus={id_karakter}',[KarakterController::class,'destroy'])->middleware('auth');
Pada route ini akan menjalankan function destroy pada KarakterController. Route ini hanya dapat diakses setelah kita login, jika belum login dan memasukkan url ini maka akan redirect otomatis ke menu login (route nomor 3). Route ini berjalan setelah klik tombol "Hapus" di route nomor 7. Route ini tidak mempunyai tampilan, route ini hanya berguna untuk menghapus data karakter dari database tabel datakarakter sesuai dengan keberadaan tombol "Hapus" tersebut. Akan redirect ke route nomor 7.

13. Route::get('/dashboard/karakter/',[KarakterController::class,'search'])->middleware('auth');
Pada route ini akan menjalankan function destroy pada KarakterController. Route ini hanya dapat diakses setelah kita login, jika belum login dan memasukkan url ini maka akan redirect otomatis ke menu login (route nomor 3). Route ini kegunaannya sama dengan route nomor 6 hanya saja data yang ditampilkan seperti route nomor 7 dan sesuai dengan hasil pencarian berdasarkan nama_karakter dari tabel datakarakter.
