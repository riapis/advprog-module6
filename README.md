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