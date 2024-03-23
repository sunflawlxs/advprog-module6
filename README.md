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