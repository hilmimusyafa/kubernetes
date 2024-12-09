## Bab 1 : Sebelum Belajar Kubernetes

### 1.1 Mulai dari Monolith dan Layanan Mikro

**Monolith** 

Model monolith adalah model aplikasi yang semua menu aplikasi, fitur apa saja ada di dalam satu model aplikasi. Model ini bersifat tradisional dan memiliki beberapa masalah, contohnya adalah jika terjadi trafik jaringan yang sangat padat maka akan membuat pelayanan menjadi lebih lambat karena hanya 1 aplikasi yang ditangani.

**Layanan Mikro (Microservices)**

Microservices adalah model aplikasi dimana aplikasi dipecah menjadi beberapa bagian, sehingga dapat menangani tugas dengan cara yang lebih baik, dan dapat berkomunikasi satu sama lain.

![Hos (1)](https://hackmd.io/_uploads/BJJN1XmsA.png)

### 1.2 Masalah Microservice

Menggunakan microservice memiliki pekerjaan rumah tersendiri dalam pengembangan, karena setiap bagian pengembangan ingin men-deploy aplikasi, aplikasi yang di-deploy banyak, tidak seperti di Monolith yang hanya satu aplikasi yang di-deploy dan langsung selesai.

Selain itu, jika ingin melakukan scale up aplikasi karena suatu hal, bagian development akan kesulitan jika melakukan scale up secara manual, tidak ada koneksi antar aplikasi. Ditambah lagi dengan aplikasi lain yang belum di scale. Hal ini akan menjadi bencana terutama bagi tim Development atau DevOps.

### Sebenarnya, ada Virtual Machine tapi...

Ya, sesuai dengan judulnya, sebenarnya ada Virtual Machine yang model arsitekturnya mirip dengan Microservice, bisa Anda lihat di gambar :

![Hos](https://hackmd.io/_uploads/BJm-yXQoC.png)

Mirip sebenarnya, namun kasus di Virtual Machine juga sama dimana scaling aplikasinya juga membutuhkan waktu yang lama dan sulit, juga untuk menjalankan Virtual Machine perlu menjalankan setiap OS yang sedang berjalan, sehingga ketika dijalankan Host OS / OS utama akan menjadi berat resource.

Namun berbeda dengan container/microservice, setiap aplikasi yang berjalan akan dikelola oleh Container Manager yang sama dan menggunakan satu OS saja.

Coba Anda perhatikan gambar :

![Hos (2)](https://hackmd.io/_uploads/B1rJ-77sA.png)

Jika Anda ingin menjalankan sebuah aplikasi, sistem akan langsung melakukan pekerjaan yang sesuai tanpa harus memikirkan dimana aplikasi tersebut disimpan.

### 1.4 Jadi, Apa Hubungannya dengan Kubernetes?

Jadi, Kubernetes adalah alat orkestrasi untuk Machine Container, bagaimana Anda memahaminya? Seperti ini... seperti sebuah orkestra di Panggung Musik, yang membutuhkan seorang konduktor untuk memimpin not dan nada. Kubernetes adalah konduktornya, dan Pemutar Musik adalah Container Aplikasi.

Jadi, sekarang saatnya belajar Kubernetes. Anda akan mempelajari Kubernetes di bab berikutnya.