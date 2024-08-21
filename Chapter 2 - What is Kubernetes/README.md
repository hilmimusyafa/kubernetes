## Chapter 2 : What is Kubernetes

### 2.1 Introduction to Kubernetes

Yes, according to the reading that has been shown previously. Kubernetes is one of the Open Source applications for automation deployment, scaling, and management of container-based applications.

Kubernetes is currently well-known and widely used, many large companies use Kubernetes.

![Hos (3)](https://hackmd.io/_uploads/rkLVLXmj0.png)

### 2.2 History of Kubernetes

Kubernetes has its roots in Google's internal Borg System, which was introduced between 2003 and 2004. Then, in 2013, Google released another project known as Omega, a flexible and scalable scheduler for large computing clusters.

### 2.3 Alur Kerja Kubernetes

In Kubernetes, the workflow will be easy, just create a configuration file and Kubernetes will take care of it. As shown in the image below :

![Hos (4)](https://hackmd.io/_uploads/rJE0K77jA.png)

### 2.4 Arsitektur Kubernetes

Arsitektur Kubernetes digambarkan seperti di bawah ini : 

![Hos (5)](https://hackmd.io/_uploads/BJOhgNQoA.png)

Jadi, kubernetes secara gamabran di atas terdapat 2 komponen, yaitu Kubernetes Master dan Kubernetes Nodes, berikut penjelasannya : 

1. Kubernetes Master
Kubernetes Master disini mengatur jalannya Nodes, yang mengatur koneksi API, yang mengatur cloud. Beginilah bagian kecilnya : 
    - kube-apiserver : yang mengatur API untuk berinteraksi dengan Kubernetes cluster.
    - etcd : database untuk menyimpan data Kubernetes Center.
    - kube-scheduler : yang memperhatikan aplikasi yang kita jalankan dan meminta node untuk menjalankan aplikasi yang dijalankan.
    - cloud-contoller-manager : yang melakukan kontrol terhadap interkasi dengan cloud provider (AWS, Azure, GCP, etc.)

2. Kubernetes Nodes
Untuk Kubernetes Nodes adalah posisi aplikasi yang dijalankan oleh Kubernetes sendiri, bagiannya yaitu : 
    - kubelet : yang bertugas memasitkan bahwa aplikasi berjalan di Node, dia berjalan di setiap node.
    - kube-proxy : yang bertugas sebagai proxy terhadap arus network dan sebagai load balancer untuk aplikasi atau node. Juga, berjalan di setiap node.
    - container-manager : bertugas memanajemen kontainer seperti docker,  containerd, cri-o, etc. Dan juga, berjalan di setiap node.
    
Jadi detail alur kerja seperti di bawah : 

![Hos (6)](https://hackmd.io/_uploads/BkYDmB7i0.png)

