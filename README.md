## What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?

- Unary: Metode RPC dimana client mengirimkan satu buah request ke server dan mendapatkan satu buah response dari server. Cocok digunakan untuk query-query yang simple dan operasi dimana satu pasangan request dan response sudah cukup. Misalnya untuk mendapatkan informasi profil pengguna

- Server Streaming: metode RPC dimana client mengirimkan satu buah request ke server dan mendapatkan serangkaian/aliran respons dari server. Cocok digunakan untuk mengambil set of data yang terlalu besar untuk ditransmisikan sekaligus

- Bi-directional Streaming: Metode RPC streaming dua arah, baik client maupun server mengirimkan serangkaian request/response yang independen menggunakan aliran read-write. Cocok digunakan untuk skenario dimana client dan server pelu mengirim banyak data secara asinkron seperti layanan chat

## What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?

- Authentication: Perlu dipertambangkan bagaimana client mangeutentikasi dirinya pada server. Di gRPC, metode autentikasi dapat berupa SSL/TSL, OAuth2, atau metode autentikasi lainnya.

- Authorization: Proses otorisasi user bisa dilakukan dengan menerapkan Role Based Access Control, Attribute Based Access Control, atau Token Based Authorization. Proses otorisasi harus dicheck secara konsisten agar tidak menimbulkan unauthorized access

- Data encryption: gRPC menggunakan SSL/TLS dan HTTP2 yang bertujuan untuk mengenkripsi data pada saat transit. Selain itu, data yang sudah di-store juga harus di enkripsi agar datanya tidak vulnerable

## What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?

Dalam skenario aplikasi chat, isu yang mungkin muncul adalah concurrency management yaitu pengelolaan message agar message ditampilkan secara terurut. Network error atau client disconnection juga harus diperhatikan dan dihandle. Message Persistence; Message juga harus bisa di store dan di-receive oleh user ketika mereka kembali online

## What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?

- Advantages: 
    - Memungkinkan operasi asinkron yaitu menghandle task lain jika suatu task sedang dikerjakan
    - Backpressure handling, jika receiver tidak bisa meng-handle banyaknya messages dari sender, maka message akan di-queue sampai batasan tertentu
    - Bekerja baik dengan komponen Tokio lainnya
- Disadvantages:
    - Karena menggunakan async programming, maka kompleksitas kode akan bertambah
    - Jika terdapat Error dari sisi sender, maka message yang mengindikasikan error tersebut harus di-design sendiri untuk dikirimkan kepada receiver
    - Hanya mendukung one-way communication dari sender ke receiver

## In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?

- Mengorganisasi kode ke dalam modules
- Menggunakan traits untuk abstraction dapat membuat kode lebih ekstensibel
- Menggunakan Structs untuk mengelompokkan data yang saling berhubungan
- Menggunakan middleware atau interceptor untuk meng-encapsulate logika yang diterapkan pada semua request atau response seperti logging, autentikasi, dan error handling

## In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?

- Memvalidasi detail-detail seperti apakah detail payment nya benar, apakah user memiliki balance yang cukup, dll. Bisa dilakukan dengan melibatkan interaksi dengan database
- Mengembalikan error message yang berbeda-beda sesuai error yang di-encounter
- Enkripsi data yang sensitive
- Menerapkan kemampuan auditing untuk menyimpan record transaksi
- Jika menggunakan service eksternal seperti payment gateway, perlu ditambahkan kode untuk berinteraksi dengan API-nya

## What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?

gRPC memungkinkan service yang ditulis dengan bahasa pemrograman yang berbeda untuk berkomunikasi satu sama lain dengan men-generate gRPC client dan server stubs. Dan karena gRPC menggunakan Contract-First Approach, developer bisa mendefine service menggunakan protocol buffers yang membuat contract antar service menjadi lebih jelas

## What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?

- Advantages:
    - Multiplexing: HTTP/2 memungkinkan beberapa request dan response untuk berada di satu TCP connection. Ini bisa menurunkan latency dari request yang menyebabkan komunikasi antara client dan server menjadi lebih cepat
    - Server Push: dengan HTTP/2, server bisa mengirimkan resource ke client secara proaktif. Ini bisa meningkatkan performance dengan mengurangi jumlah round-trip yang dibutuhkan
    - Header Compression: HTTP/2 menggunakan HPACK compression untuk headers sehingga bisa menurunkan overhead untuk request yang memerlukan headers yang yang besar
- Disadvantages:
    - HTTP/2 lebih complex daripada HTTP/1.1 sehingga membuat implementasi dan debug menjadi lebih susah
    - HTTP/2 bisa mengonsumsi banyak resources seperti CPU dan memori karena proses tambahan yang diperlukan untuk multiplexing, header compression, dan lain-lain.

## How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?

REST API menggunakan request-response model, dimana client mengirimkan request dan menunggu response dari server. Agar Real-Time Communication menggunakan REST API dapat tercapai, teknik seperti long-polling, server-sent event, atau Websocket, tetapi ini bisa meningkatkan kompleksitas dan latency sehingga REST API tidak terlalu ideal digunakan untuk real-time use cases. gRPC mendukung bidrectional streaming, yang memungkinkan client dan server untuk mengirimkan data secara independen dan asinkronus. Ini lebih efisien daripada request-response model karena server dan client bisa mengirimkan data selama data itu sudah available sehingga dapat menurunkan latency dan meningkatkan responsiveness. Dengan ini, gRPC lebih cocok untuk digunakan untuk real-time use cases dimana update dikirim secara kontinu

## What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?

- Protocol Buffers mengharuskan data untuk mengikuti struktur yang sudah didefinisikan. Ini bisa menurunkan jumlah runtime error yang muncul karena inkonsistensi data. Dengan JSON, error bisa menjadi lebih sering bermunculan karena tingkat kebebasan yang ditawarkannya
- Protocol buffer mendukung backward dan forward compatibility dan perubahan pada skema dapat dikelola secara lebih ketat. Sedangkan, dengan JSON mungkin diperlukan usaha ekstra untuk mengelola perubahan skema dan memastikan backforward compatibility
- Protocol Buffers lebih efisien dalam hal serialization dan deserialization dibanding JSON karena protobuf menggunakan binary encoding format yang menyebabkan message dengan ukuruan kecil bisa diproses secara lebih cepat
