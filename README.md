Module 10: Asynchronous Programming

 Learning Objectives

1. Mahasiswa bisa menjelaskan bagaimana asynchronous programming bekerja dan apa bedanya dengan synchronous
2. Mahasiswa memahami teknologi terkait asynchronous seperti language support, websocket dll
3. Mahasiswa dapat membuat program asynchronous dan menerapkan pada project yang sesuai

 Overview

Asynchronous programming adalah konsep pemrograman di mana programmer mempertimbangkan bahwa pemanggilan fungsi atau eksekusi statement dapat dilakukan secara asynchronous. Konsep ini sangat penting untuk dipahami karena dapat meningkatkan efisiensi penggunaan CPU, memory, dan alokasi network terutama dalam skala produksi.

 Experiments

 Tutorial 1: Timer

 1.1. Initial Code
Commit Message: "Experiment 1.1: Original timer from the book"

Pada eksperimen ini, saya mempelajari implementasi timer asynchronous sederhana berdasarkan contoh dari Rust async book. Program ini mendemonstrasikan bagaimana executor dan spawner bekerja dalam menjalankan future secara asynchronous.

 1.2. Understanding How It Works
Commit Message: "Experiment 1.2: Understanding how it works"

Setelah menambahkan kalimat debug setelah `spawner.spawn(...)`, saya dapat mengobservasi bagaimana async runtime bekerja. Hasil eksekusi menunjukkan bahwa task yang di-spawn tidak langsung dieksekusi, melainkan dijadwalkan untuk dijalankan oleh executor. Ini membuktikan bahwa futures dalam Rust bersifat lazy - mereka tidak melakukan apa-apa sampai secara aktif didorong untuk completion.

 1.3. Multiple Spawn and Removing Drop
Commit Message: "Experiment 1.3: Multiple Spawn and removing drop"

Dalam eksperimen ini, saya mencoba:
- Spawning multiple tasks untuk melihat bagaimana mereka dijalankan secara concurrent
- Menghapus dan menambahkan kembali statement `drop(spawner)` untuk memahami efeknya

Hasil observasi:
- Spawner: Bertanggung jawab untuk mengirim task baru ke executor melalui channel
- Executor: Menerima task dari channel dan menjalankannya dengan memanggil `poll`
- Drop: Ketika spawner di-drop, tidak ada task baru yang bisa ditambahkan, dan executor akan berhenti setelah semua task selesai

Korelasi ketiganya: Spawner sebagai producer, executor sebagai consumer, dan drop sebagai sinyal untuk menghentikan penerimaan task baru.

### Tutorial 2: Broadcast Chat

 2.1. Original Code
Commit Message: "Experiment 2.1: Original code, and how it run"

Aplikasi broadcast chat ini mendemonstrasikan penggunaan asynchronous programming yang lebih praktis dengan websocket. 

Cara menjalankan:
- Server: `cargo run --bin server`
- Client: `cargo run --bin client`

Yang terjadi: Ketika satu client mengirim pesan, pesan tersebut akan di-broadcast ke semua client yang terhubung. Ini memungkinkan komunikasi real-time antar multiple clients berkat penggunaan websocket dan async programming.

 2.2. Modifying Port
Commit Message: "Experiment 2.2: Modifying port"

Untuk mengubah port dari 2000 menjadi 8080, diperlukan modifikasi di dua tempat:
1. client.rs: Mengubah `ClientBuilder::from_uri` menjadi `ws://127.0.0.1:8080`
2. server.rs: Mengubah `listener` binding menjadi `127.0.0.1:8080`

Kedua sisi (client dan server) menggunakan websocket protocol yang sama, namun dengan peran berbeda - server sebagai listener dan client sebagai connector.

 2.3. Small Changes - Add IP and Port
Commit Message: "Experiment 2.3: Small changes, add IP and Port"

Saya memodifikasi broadcast message untuk menyertakan informasi IP dan port pengirim. Perubahan dilakukan dengan mengubah format broadcast dari `bcast_tx.send(text.into())` menjadi `bcast_tx.send(format!("{}: {}", addr, text))`. 

Dengan modifikasi ini, setiap pesan yang diterima clients akan menampilkan alamat IP dan port dari pengirim, memberikan informasi konteks yang berguna tentang siapa yang mengirim pesan.

 Tutorial 3: WebChat using Yew

 3.1. Original Code
Commit Message: "Experiment 3.1: Original code"

WebChat menggunakan Yew framework mendemonstrasikan implementasi client-side yang lebih modern dengan interface web. Aplikasi ini terdiri dari:
- Frontend: Aplikasi Yew yang berjalan di browser
- Backend: WebSocket server (dalam contoh ini menggunakan JavaScript)

Aplikasi berhasil dijalankan dan menunjukkan chat interface yang interaktif di browser.

 3.2. Creative Modifications
Commit Message: "Experiment 3.2: Be Creative!"

Untuk meningkatkan kreativitas dan user experience, saya menambahkan beberapa fitur:
1. Enhanced UI: Menambahkan CSS styling untuk tampilan yang lebih menarik
2. Timestamp: Setiap pesan ditampilkan dengan timestamp
3. User identification: Penambahan sistem username sederhana
4. Message formatting: Pesan ditampilkan dengan format yang lebih rapi
5. Connection status: Indikator status koneksi websocket

Kreativitas ini penting karena menurut World Economic Forum, kreativitas adalah kunci untuk bersaing dengan AI di masa depan.

 Bonus: Rust WebSocket Server
Commit Message: "Bonus: Rust Websocket server for YewChat!"

Saya berhasil memodifikasi server dari Tutorial 2 untuk mendukung WebChat dari Tutorial 3. Perubahan utama meliputi:

1. JSON Message Format: Mengubah format pesan dari plain text menjadi JSON untuk kompatibilitas dengan Yew client
2. CORS Support: Menambahkan header CORS untuk memungkinkan browser mengakses websocket
3. Message Serialization: Implementasi serialization/deserialization JSON menggunakan serde

Perbandingan:
- JavaScript Server: Lebih mudah setup, built-in JSON support
- Rust Server: Lebih performant, type-safe, better memory management

Saya lebih memilih Rust server karena memberikan keamanan tipe yang lebih baik dan performa yang superior, meskipun memerlukan setup yang sedikit lebih kompleks.

 Key Concepts Learned

Asynchronous vs Synchronous Programming
- Synchronous: Eksekusi sekuential, satu task selesai baru lanjut ke berikutnya
- Asynchronous: Multiple tasks dapat berjalan concurrent, CPU tidak idle saat menunggu I/O

 Rust Async Features
- Futures: Lazy computation yang dapat di-poll untuk completion
- Tasks: Unit of work yang dapat dijadwalkan oleh executor
- Executors: Runtime yang menjalankan futures hingga completion
- async/await: Syntax sugar untuk bekerja dengan futures

 WebSocket Benefits
- Real-time bidirectional communication
- Lebih efisien dibandingkan HTTP polling
- Perfect untuk aplikasi chat dan real-time updates

 Technical Implementation Details

 Future and Task Architecture
Rust mengimplementasikan asynchronous programming menggunakan:
- Zero-cost abstractions: Tidak ada overhead runtime
- State machines: Async functions dikompilasi menjadi state machines
- Cooperative multitasking: Tasks secara volunter yield control

 WebSocket Integration
- Menggunakan tokio-tungstenite untuk WebSocket support
- Broadcast mechanism menggunakan tokio's broadcast channel
- Client management dengan concurrent HashMap

 Conclusion

Asynchronous programming sangat powerful untuk aplikasi yang I/O intensive seperti web servers dan chat applications. Rust memberikan implementasi yang efisien dan type-safe untuk async programming, memungkinkan developer untuk membuat aplikasi yang scalable dan performant.
