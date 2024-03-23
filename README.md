> Fari Hafizh Ramadhan - 2206083691

# Modul 6: Concurrency

## Commit 1 Reflection notes

handle_connection method, fungsi ini menerima koneksi TCP melalui parameter stream. Selanjutnya, koneksi tersebut dibaca menggunakan BufReader untuk mempermudah pembacaan data dari stream.

1. `BufReader::new(&mut stream)`: Ini membuat BufReader baru yang akan membaca dari koneksi TCP yang disediakan.
2. `buf_reader.lines()`: Metode ini mengembalikan iterator yang akan menghasilkan baris-baris teks dari BufReader. Ini akan membaca data dari koneksi TCP, baris per baris.
3. `.map(|result| result.unwrap())`: Ini memetakan setiap hasil baris yang diperoleh dari iterator untuk menyingkirkan lapisan hasil Result dan mengambil nilai teks aktual. unwrap() digunakan di sini karena kita mengabaikan kemungkinan kesalahan.
4. `.take_while(|line| !line.is_empty())`: Ini mengambil baris-baris dari iterator selama baris tidak kosong. Ini berhenti mengambil baris saat menemui baris kosong.
5. `.collect()`: Ini mengumpulkan baris-baris yang telah dibaca menjadi sebuah Vec<_> yang disebut http_request.
6. `println!("Request: {:#?}", http_request)`: Ini mencetak http_request ke konsol dalam format debug ({:#?}). Ini berguna untuk melihat struktur data dari vektor http_request dengan cara yang lebih terstruktur.

## Commit 2 Reflection notes
![Commit 2 screen capture](/img/commit2.png) 

Pada kode yang baru ditambahkan ke method handle_connection,

```
let status_line = "HTTP/1.1 200 OK"; 
    let contents = fs::read_to_string("hello.html").unwrap(); 
    let length = contents.len(); 
 
    let response = 
        format!("{status_line}\r\nContent-Length: 
{length}\r\n\r\n{contents}"); 
 
    stream.write_all(response.as_bytes()).unwrap();
```
Baris-baris ini bertugas menghasilkan sebuah reponse HTTP yang akan dikirimkan kembali ke klien. Response ini terdiri dari status_line, panjang konten, dan konten HTML dari file hello.html. Setelah response tersebut dibuat, ia dikirimkan ke klien melalui koneksi TCP menggunakan baris kode `stream.write_all(response.as_bytes()).unwrap();`.

## Commit 3 Reflection notes
![Commit 3 screen capture](/img/commit3.png) 

Berdasarkan langkah-langkah yang dijelaskan pada Chapter 20:

- Kita dapat memisahkan respons yang diberikan dengan melakukan pengecekan pada request_line apakah sama dengan request line ke path /. Jika iya, akan mengirimkan hello.html, jika tidak, akan ke 404.html
- Kode yang awalnya ditulis perlu di-refactor karena masih terdapat duplikasi kode. Dengan melakukan refactoring, blok if-else hanya akan berisi kode yang membedakan kedua kondisi saja dan menghilangkan kode yang bersifat redundan

## Commit 4 Reflection notes
Dalam simulasi ini, ada 2 jenis request yang dapat dilakukan: path `/` dan `/sleep`. Jika kita mengakses path `/`, 
halaman akan segera dimuat, sedangkan path `/sleep` akan menunda pemrosesan selama 10 detik sebelum halaman dimuat. Namun, 
jika kita mengakses path `/sleep` terlebih dahulu, dan kemudian path `/`, keduanya akan mengalami penundaan selama 10 detik 
karena path `/` harus menunggu sampai permintaan `/sleep` selesai diproses. Hal ini disebabkan oleh sifat single-threaded 
server, yang berarti bahwa server hanya dapat memproses satu permintaan pada satu waktu. Server tidak akan memproses 
request kedua sebelum permintaan pertama selesai. Situasi ini dapat menjadi lebih buruk jika jumlah request bertambah.

## Commit 5 Reflection notes
Untuk menerapkan konsep multithreading dalam program, salah satu pendekatan yang dapat digunakan adalah dengan menggunakan 
ThreadPool. ThreadPool adalah sebuah kumpulan thread yang telah diinisialisasi dan siap untuk mengeksekusi tugas. Ketika 
satu thread sedang menjalankan suatu permintaan, thread lain dalam ThreadPool dapat menjalankan permintaan lain tanpa 
harus menunggu sampai permintaan pertama selesai. Dengan demikian, kita dapat menangani beberapa permintaan secara bersamaan, 
meningkatkan throughput dari program. Namun, jumlah thread dalam ThreadPool dibatasi (misalnya, hanya ada 4 thread dalam contoh ini) 
untuk melindungi program dari serangan Denial of Service (DoS).

## Commit Bonus Reflection notes
![Commit Bonus screen capture](/img/commitB.png)
Dalam mengikuti langkah pada Bab 12, saya berupaya meningkatkan fungsi method new dengan membuat method baru bernama build 
dan menerapkan penanganan kesalahan yang lebih baik. Hal ini dilakukan karena umumnya method new diharapkan tidak mengalami kegagalan. 
Oleh karena itu, saya menggantinya dengan penggunaan method build yang akan menangani error yang mungkin terjadi pada method 
new sebelumnya, khususnya ketika ukuran ThreadPool dibuat dengan ukuran 0. Dengan menggunakan method build ini, penanganan 
kesalahan dapat dilakukan dengan lebih baik, dengan mengembalikan hasil berupa Result yang akan berisi ThreadPool jika berhasil, 
dan memberikan pesan error jika gagal.