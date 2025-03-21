## Module 6 Reflection
Fadhli Raihan Ardiansyah 
2306207594
## Commit 1 Reflection Notes
Method `handle_connection` digunakan untuk menangani koneksi klien melalui objek `TcpStream`. `BufReader` digunakan untuk membaca stream data secara efisien menggunakan proses buffering. Data yang diterima kemudian diproses dengan method `.lines()` yang menghasilkan iterator dari setiap baris yang kemudian dibaca sebagai `Result`. Setiap baris yang berhasil dibaca akan diproses menggunakan map untuk menghapus pembungkus `Result`, dan `take_while` digunakan untuk mengambil baris-barusnya sampai kita menemukan baris kosong yang menandakan akhir dari header HTTP. Semua baris yang dibaca ini lalu disimpan dalam `Vec<String>` yang berfungsi untuk menyimpan seluruh permintaan HTTP dari klien.

## Commit 2 Reflection Notes
Perubahan terbaru pada `handle_connection` menambahkan pembuatan dan pengiriman response HTTP setelah memproses request yang diterima. Sebelumnya, `handle_connection` hanya membaca request menggunakan `BufReader` dan menyimpannya dalam sebuah vektor. Sekarang, setelah request diproses, server membangun response HTTP lengkap dengan status "200 OK", mengukur panjang konten yang dibaca dari file `hello.html`, dan menambahkan informasi tersebut ke dalam header `Content-Length`. Setelah itu, response ini dikirimkan kembali ke klien melalui stream yang sama dengan menggunakan `write_all()`. Perubahan ini membuat `handle_connection` dapat merespons permintaan klien dengan template HTML.

