## Module 6 Reflection
Fadhli Raihan Ardiansyah 
2306207594
## Commit 1 Reflection Notes
Method `handle_connection` digunakan untuk menangani koneksi klien melalui objek `TcpStream`. `BufReader` digunakan untuk membaca stream data secara efisien menggunakan proses buffering. Data yang diterima kemudian diproses dengan method `.lines()` yang menghasilkan iterator dari setiap baris yang kemudian dibaca sebagai `Result`. Setiap baris yang berhasil dibaca akan diproses menggunakan map untuk menghapus pembungkus `Result`, dan `take_while` digunakan untuk mengambil baris-barusnya sampai kita menemukan baris kosong yang menandakan akhir dari header HTTP. Semua baris yang dibaca ini lalu disimpan dalam `Vec<String>` yang berfungsi untuk menyimpan seluruh permintaan HTTP dari klien.

## Commit 2 Reflection Notes
![Commit 2 screen capture](/assets/images/commit2.png)
Perubahan terbaru pada `handle_connection` menambahkan pembuatan dan pengiriman response HTTP setelah memproses request yang diterima. Sebelumnya, `handle_connection` hanya membaca request menggunakan `BufReader` dan menyimpannya dalam sebuah vektor. Sekarang, setelah request diproses, server membangun response HTTP lengkap dengan status "200 OK", mengukur panjang konten yang dibaca dari file `hello.html`, dan menambahkan informasi tersebut ke dalam header `Content-Length`. Setelah itu, response ini dikirimkan kembali ke klien melalui stream yang sama dengan menggunakan `write_all()`. Perubahan ini membuat `handle_connection` dapat merespons permintaan klien dengan template HTML.

## Commit 3 Reflection notes
Perubahan di `handle_connection`  dimulai dengan penambahan logika untuk memeriksa request dan menentukan response berdasarkan status yang diterima, yaitu "200 OK" untuk permintaan ke halaman utama `(GET / HTTP/1.1)` dan "404 NOT FOUND" untuk request lainnya. Sebelumnya, kode tersebut memiliki dua blok terpisah untuk masing-masing kondisi status, yang menyebabkan duplikasi kode. Dengan refaktoring, proses pemilihan status dan nama file dipisahkan menggunakan destrukturisasi `let (status_line, filename)`, sehingga hanya ada satu bagian kode untuk membaca file dan membangun response, yang mengurangi duplikasi kode.  Refaktoring ini juga membuat kode lebih efisien dengan hanya membaca dan memproses file sekali, mengurangi penggunaan memori yang tidak perlu, dan membuat kode lebih mudah dipelihara. Refaktoring ini juga membuat kode lebih efisien dengan hanya membaca dan memproses file sekali dan membuat kode lebih mudah dipelihara. Kini, jika ada penambahan status baru atau perubahan cara penanganan response, kita hanya perlu mengubah satu bagian kode tanpa menduplikasi logika di beberapa tempat.

## Commit 4 Reflection notes
Simulasi slow request pada perubahan terbaru `handle_connection` memberikan gambaran mengenai bagaimana server yang berjalan secara synchronous dapat mengalami hambatan dalam melayani banyak koneksi sekaligus. Ketika request dengan path `/sleep` diterima, server menjalankan perintah `thread::sleep(Duration::from_secs(10))` yang menyebabkan thread utama berhenti selama 10 detik. Ini berarti selama 10 detik tersebut, server tidak bisa memproses request lainnya, sehingga jika ada koneksi baru seperti ke halaman utama `(GET / HTTP/1.1)`, koneksi tersebut harus menunggu hingga proses sleep selesai. Kondisi ini terjadi karena server menggunakan pendekatan single-threaded yang menangani setiap koneksi satu per satu, sehingga satu request yang lambat dapat memblokir request lain. Refactoring diperlukan untuk mengatasi masalah ini, yaitu dengan mengubah arsitektur server menjadi multi-threaded, sehingga satu request yang lambat tidak akan menghambat request lainnya.

## Commit 5 Reflection notes
Perubahan pada kode adalah implementasi `ThreadPool` yang memungkinkan server menangani banyak request secara bersamaan tanpa harus membuat thread baru untuk setiap koneksi. Dengan adanya `ThreadPool`, worker thread diciptakan terlebih dahulu dan siap menerima job, sehingga setiap request yang masuk dapat langsung disalurkan ke worker yang tersedia. Hal ini meningkatkan efisiensi penggunaan resource karena jumlah thread yang berjalan sudah terbatas dan dikelola dengan baik. `ThreadPool` menggunakan channel `mpsc` yang dibungkus dengan `Arc` dan `Mutex` untuk mengamankan komunikasi antara thread utama dan worker. Setiap tugas dikirim melalui channel, sehingga worker dapat mengambil tugas tersebut dan menjalankannya secara bergantian. Setiap worker menjalankan infinite loop yang selalu memeriksa adanya job baru di channel. Saat menerima job, worker langsung mengeksekusi tugas tersebut dan kemudian kembali menunggu job selanjutnya. Pendekatan ini menjaga agar server tetap responsif, meskipun terdapat request yang membutuhkan waktu proses lama, seperti simulasi slow request.