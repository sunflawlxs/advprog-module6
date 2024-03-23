commit 1

Apa fungsi dari handle_connection?
Fungsi handle_connection bertugas untuk memproses aliran TCP yang masuk. Berikut adalah penjelasan mengenai apa yang dilakukannya:
1. Fungsi ini menerima sebuah referensi mutable ke TcpStream sebagai argumennya.
2. Kemudian, fungsi ini membuat BufReader yang membungkus aliran TCP yang diberikan. Ini membantu dalam membaca dari aliran dengan cara yang lebih efisien.
3. Fungsi ini membaca baris demi baris dari pembaca terbuffer (BufReader) sampai menemukan baris kosong. Ini adalah format umum dari permintaan HTTP, di mana header diikuti oleh sebuah baris kosong.
4. Fungsi ini mengumpulkan baris-baris tersebut ke dalam sebuah vektor, yang merepresentasikan permintaan HTTP.
5. Terakhir, fungsi ini mencetak permintaan HTTP tersebut untuk diperiksa.
Singkatnya, handle_connection membaca aliran TCP yang masuk baris demi baris sampai menemukan sebuah baris kosong yang menandakan akhir dari header permintaan HTTP. Kemudian, fungsi ini mencetak baris-baris yang telah dikumpulkan sebagai permintaan HTTP.


Commit 2
Menambahkan kode tambahan dalam fungsi `handle_connection` memodifikasinya untuk mengirimkan balasan HTTP kembali ke klien. Berikut adalah penjelasan tentang apa yang dilakukan oleh kode baru tersebut:  
1. Kode tersebut mendefinisikan status_line yang menunjukkan bahwa balasan HTTP adalah `200 OK`. Ini berarti permintaan berhasil.  
2. Kode tersebut membaca isi dari sebuah file bernama `hello.html` menjadi sebuah string menggunakan `fs::read_to_string()`. Ini diasumsikan bahwa terdapat sebuah file bernama `hello.html` di dalam direktori yang sama dengan executable.  
3. Kode tersebut menghitung panjang dari string konten.  
4. Kode tersebut memformat balasan HTTP, termasuk baris status, panjang konten, dan isi dari file `hello.html`.  
5. Kode tersebut menuliskan balasan kembali ke aliran TCP, mengirimkannya ke klien menggunakan `write_all()`.  
Jadi, secara ringkas, fungsi `handle_connection` yang dimodifikasi membaca konten dari file `hello.html`, membuat sebuah balasan HTTP dengan status line `200 OK`, dan mengirimkannya kembali ke klien melalui aliran TCP.
<p align="center">
  <img src="resources\commit2.png" />
</p>

commit 3
1. Saya membuat file `404.html` seperti berikut. 
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Hello!</title>
  </head>
  <body>
    <h1>Oops!</h1>
    <p>Sorry, I don't know what you're asking for.</p>
    <p>Rust is running from Sheryl's machine.</p>
  </body>
</html>
``` 
2. Memodifikasi fungsi `handle_connection` seperti berikut. 
```rust
fn handle_connection(mut stream: TcpStream) {
    let buf_reader = BufReader::new(&mut stream);
    let request_line = buf_reader.lines().next().unwrap().unwrap();

    if request_line == "GET / HTTP/1.1" {
        let status_line = "HTTP/1.1 200 OK";
        let contents = fs::read_to_string("hello.html").unwrap();
        let length = contents.len();

        let response = format!(
            "{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}"
        );

        stream.write_all(response.as_bytes()).unwrap();
    } else {
        let status_line = "HTTP/1.1 404 NOT FOUND";
        let contents = fs::read_to_string("404.html").unwrap();
        let length = contents.len();

        let response = format!(
            "{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}"
        );

        stream.write_all(response.as_bytes()).unwrap();
    }
}
```
3. melakukan refactor pada fungsi `handle_connection` seperti berikut.
```rust
fn handle_connection(mut stream: TcpStream) {
    let buf_reader = BufReader::new(&mut stream);
    let request_line = buf_reader.lines().next().unwrap().unwrap();

    let (status_line, filename) = if request_line == "GET / HTTP/1.1" {
        ("HTTP/1.1 200 OK", "hello.html")
    } else {
        ("HTTP/1.1 404 NOT FOUND", "404.html")
    };
    let contents = fs::read_to_string(filename).unwrap();
    let length = contents.len();

    let response = format!(
        "{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}"
    );
    stream.write_all(response.as_bytes()).unwrap();
}
```
4. tampilan dari `404.html`
<p align="center">
  <img src="resources\commit3.png" />
</p>

Fungsi `handle_connection` yang dimodifikasi bertindak sebagai server HTTP sederhana. Ia mengambil baris permintaan dari klien dalam permintaan HTTP dan memeriksa apakah itu adalah permintaan `GET / HTTP/1.1`, yang menunjukkan permintaan untuk jalur root (/). Berdasarkan hal ini, fungsi memberikan respons dengan konten yang berbeda:

1. Jika permintaan adalah untuk jalur root (GET / HTTP/1.1), fungsi akan merespons dengan status baris `HTTP/1.1 200 OK`. Kemudian, ia membaca konten dari file `hello.html`, menghitung panjangnya, membuat respons HTTP yang berisi konten tersebut, dan mengirimkannya kembali ke klien.

2. Jika permintaan bukan untuk jalur root (menunjukkan bahwa sumber daya yang diminta tidak ditemukan), fungsi akan merespons dengan status baris `HTTP/1.1 404 NOT FOUND`. Setelah itu, ia membaca konten dari file `404.html`, menghitung panjangnya, membuat respons HTTP yang berisi konten tersebut, dan mengirimkannya kembali ke klien.

Dengan demikian, fungsi `handle_connection` yang telah dimodifikasi memberikan respons dengan konten yang berbeda tergantung pada permintaan yang diterima. Jika permintaan adalah untuk jalur root, server akan memberikan respons dengan `hello.html`. Jika tidak, server akan memberikan respons dengan halaman `404.html` yang menunjukkan bahwa sumber daya yang diminta tidak ditemukan.

commit 4
Pada COMMIT 4:

1. `handle_connection` masih mengambil baris permintaan dari klien seperti sebelumnya.
2. Namun, kali ini menggunakan pemodelan pola untuk memeriksa berbagai jenis baris permintaan.
3. Jika permintaan adalah `GET / HTTP/1.1`, server akan merespons dengan status `HTTP/1.1 200 OK` dan konten dari `hello.html`.
4. Jika permintaan adalah `GET /sleep HTTP/1.1`, server akan tidur selama 10 detik sebelum memberikan respons dengan status `HTTP/1.1 200 OK` dan konten dari `hello.html`.
5. Jika permintaan tidak cocok dengan pola yang ditentukan, server akan merespons dengan status `HTTP/1.1 404 NOT FOUND` dan konten dari `404.html`.
6. Setelah menentukan respons yang sesuai, server membaca konten dari file yang dibutuhkan.
7. Panjang konten dihitung.
8. Respons HTTP dibangun dengan informasi yang diperlukan.
9. Akhirnya, respons dikirim kembali ke klien melalui aliran TCP.

Hal ini memperluas kemampuan server untuk menangani permintaan khusus di mana server akan tidur sejenak sebelum memberikan respons jika permintaan adalah `GET /sleep HTTP/1.1`. Selain itu, server tetap melayani permintaan untuk `hello.html` atau memberikan tanggapan 404 jika sumber daya tidak ditemukan.

commit 5
Berikut adalah cara kerjanya:

1. Inisialisasi ThreadPool (`ThreadPool::new`):
   - Sebuah thread pool baru disiapkan dengan ukuran tertentu, yang menentukan jumlah thread pekerja.
   - Ini mendirikan sebuah saluran (channel) (`mpsc::channel`) untuk komunikasi antara thread utama (yang mengirimkan pekerjaan) dan thread pekerja.
   - Sebuah vektor dibuat untuk menyimpan thread pekerja.
   - Untuk setiap thread pekerja, itu membuat struktur Worker baru, meneruskan ID dan salinan ujung penerima saluran.

2. Pembuatan Worker (`Worker::new`):
   - Setiap thread pekerja diinisialisasi dengan sebuah penutup yang berjalan dalam loop tak terbatas.
   - Dalam loop, pekerja menunggu pekerjaan dikirim melalui saluran.
   - Begitu pekerjaan diterima, pekerja mengeksekusinya.

3. Pelaksanaan Pekerjaan (`ThreadPool::execute`):
   - Untuk menjalankan sebuah pekerjaan, thread utama mengemas pekerjaan dalam sebuah kotak dan mengirimkannya melalui saluran ke salah satu thread pekerja.
   - Thread pekerja yang ditunjuk kemudian mengeksekusi pekerjaan tersebut.

4. Conccurency dan Komunikasi:
   - `Arc<Mutex<mpsc::Receiver<Job>>>` dibagikan di antara semua thread pekerja, memungkinkan akses aman ke ujung penerima saluran.
   - `Mutex` memastikan akses eksklusif untuk menerima pekerjaan oleh hanya satu thread pekerja pada satu waktu, mencegah perlombaan data.
   - Saluran `mpsc` (multi-penghasil, tunggal-penerima) memungkinkan beberapa thread untuk secara bersamaan mengirimkan pekerjaan ke thread pekerja sambil menjamin bahwa setiap pekerjaan hanya diproses oleh satu pekerja.

Secara esensial, thread pool mengawasi sejumlah tetap thread pekerja. Tugas-tugas diserahkan ke pool dan dieksekusi secara bersamaan oleh thread-thread pekerja ini, menyediakan mekanisme yang mudah untuk eksekusi tugas secara paralel.