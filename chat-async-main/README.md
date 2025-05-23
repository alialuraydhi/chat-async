## How to run and what happens?

![image](https://github.com/user-attachments/assets/61ef0f54-85b5-4ebf-90d4-637455344ce9)

Untuk menjalankan task satu server dan tiga client, cukup buka empat terminal masing-masing untuk satu task. Cara runnya,
- Server : `cargo run --bin server`
- Client : `cargo run --bin client`

Terlihat bahwa tiap message yang kita kirim melalui salah satu client, akan diproses oleh server dan akan direturn ke semua client. Perilaku ini sama seperti *broadcast mechanism*.  

## Modifying port

![image](https://github.com/user-attachments/assets/07d3b973-e26f-4372-a9c0-f52231d002b8)

Untuk mengubah port dari 2000 menjadi 8080, ada dua hal utama yang harus diubah
1. Pada `client.rs`, ubah `ClientBuilder` menjadi `ClientBuilder::from_uri(Uri::from_static("ws://127.0.0.1:8080"))`
2. Pada `server.rs`, ubah `listener`-nya dengan `let listener = TcpListener::bind("127.0.0.1:8080").await?;`
3. (Opsional) Pada `server.rs`, ubah pesan setelah *binding port* menjadi `println!("listening on port 8080");`

Dengan demikian, ketika kita menjalankan server dan client, sistem akan berjalan di port `8080`.

## Add IP and Port

![image](https://github.com/user-attachments/assets/2fca4c74-e818-44c3-8a07-7e6e7d9dbac9)

Pada commit ini, untuk menambahkan IP dan Port client yang mengirim *broadcast message* , kita hanya perlu mengubah
`bcast_tx.send(text.into())?;` menjadi `bcast_tx.send(format!("{}: {}", addr, text))?;`. Dengan demikian, tiap *broadcast message* akan memiliki informasi address pengirim.
