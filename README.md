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