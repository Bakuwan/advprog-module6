## Module 6 Reflection
Fadhli Raihan Ardiansyah 
2306207594
## (1) handle_connection method
Method `handle_connection` digunakan untuk menangani koneksi klien melalui objek `TcpStream`. `BufReader` digunakan untuk membaca stream data secara efisien menggunakan proses buffering. Data yang diterima kemudian diproses dengan method `.lines()` yang menghasilkan iterator dari setiap baris yang kemudian dibaca sebagai `Result`. Setiap baris yang berhasil dibaca akan diproses menggunakan map untuk menghapus pembungkus `Result`, dan `take_while` digunakan untuk mengambil baris-barusnya sampai kita menemukan baris kosong yang menandakan akhir dari header HTTP. Semua baris yang dibaca ini lalu disimpan dalam `Vec<String>` yang berfungsi untuk menyimpan seluruh permintaan HTTP dari klien.
