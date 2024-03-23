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
<p align="center">
  <img src="resources\commit2.png" />
</p>