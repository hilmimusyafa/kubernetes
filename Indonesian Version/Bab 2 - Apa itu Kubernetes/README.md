## Bab 2 : Apa itu Kubernetes

### 2.1 Pendahuluan Kubernetes

Ya, sesuai dengan bacaan yang telah ditampilkan sebelumnya. Kubernetes adalah salah satu aplikasi Open Source untuk penerapan otomatisasi, penskalaan, dan pengelolaan aplikasi berbasis kontainer.

Kubernetes saat ini sudah sangat terkenal dan banyak digunakan, banyak perusahaan besar yang menggunakan Kubernetes.

![Hos (3)](https://hackmd.io/_uploads/rkLVLXmj0.png)

### 2.2 Sejarah Kubernetes

Kubernetes berakar dari Sistem Borg internal Google, yang diperkenalkan antara tahun 2003 dan 2004. Kemudian, pada tahun 2013, Google merilis proyek lain yang dikenal sebagai Omega, sebuah penjadwal yang fleksibel dan dapat diskalakan untuk cluster komputasi besar.

### 2.3 Alur Kerja Kubernetes

Dalam Kubernetes, alur kerjanya akan mudah, cukup buat file konfigurasi dan Kubernetes akan mengurusnya. Seperti yang ditunjukkan pada gambar di bawah ini:

![Hos (4)](https://hackmd.io/_uploads/rJE0K77jA.png)

### Gambar 2.4 Arsitektur Kubernetes

Arsitektur Kubernetes digambarkan seperti di bawah ini: 

![Hos (5)](https://hackmd.io/_uploads/BJOhgNQoA.png)

Jadi, kubernetes secara gamabran di atas terdapat 2 komponen, yaitu Kubernetes Master dan Kubernetes Nodes, berikut penjelasannya: 

1. Kubernetes Master
Kubernetes Master disini mengatur jalannya Nodes, yang mengatur koneksi API, yang mengatur cloud. Beginilah bagian kecilnya: 
    - kube-apiserver : yang mengatur API untuk berinteraksi dengan Kubernetes cluster.
    - etcd : database untuk menyimpan data Kubernetes Center.
    - kube-scheduler : yang memperhatikan aplikasi yang kita jalankan dan meminta node untuk menjalankan aplikasi yang dijalankan.
    - cloud-contoller-manager : yang melakukan kontrol terhadap interkasi dengan cloud provider (AWS, Azure, GCP, dll)

2. Kubernetes Nodes
Untuk Kubernetes Nodes adalah posisi aplikasi yang dijalankan oleh Kubernetes sendiri, bagiannya yaitu: 
    - kubelet : yang bertugas memastikan bahwa aplikasi berjalan di Node, dia berjalan di setiap node.
    - kube-proxy : yang bertugas sebagai proxy terhadap arus network dan sebagai load balancer untuk aplikasi atau node. Juga, berjalan di setiap node.
    - container-manager : bertugas memanajemen kontainer seperti docker, containerd, cri-o, dll. Dan juga, berjalan di setiap node.
    
Jadi detail alur kerjanya seperti di bawah ini: 

![Hos (6)](https://hackmd.io/_uploads/BkYDmB7i0.png)