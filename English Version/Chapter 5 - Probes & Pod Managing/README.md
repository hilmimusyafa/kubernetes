## Chapter 5 : Probes & Pod Managing

### 5.1 Probes

Probes merupakan prosedur pengecekan yang ada di Kubernetes. Probes di Kubernetes dibagi 3, yaitu Liveness, Readiness, dan Startup. Berikut penjelasannya : 

#### 5.1.1 Macam - macam Probes

1. Liveness Probe

    Liveness Probe digunakan oleh Kubelet untuk mengecek kapan perlu me-restart Pod. Misal ketika liveness probe pada Pod tidak merespon kubelet maka akan secara otomatis me-restart probe.

2. Readiness Probe

    Kubelet menggunakan Readiness Probe untuk mengecek apakah Pod siap menerima traffic.

3. Startup Probe

    Kubelet menggunakan Startup Probe untuk mengecek apakah Pod sudah berjalan, Jika belum berjalan, maka kubelet tidak akan melakukan pengecekan liveness dan readiness. Startup probe cocok untuk Pod yang membutuhkan proses startup lama, ini dapat digunakan untuk memastikan Pod tidak mati oleh kubelet sebelum selesai berjalan dengan sempurna.
    
#### 5.1.2 Mekanisme Pengecekan Probe

- HTTP Get
- TCP Socket
- Command Exec

#### 5.1.3 Template Probe Pada Kubernetes

5.1.3.1-template-probe-pod.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-name
  labels:
    label-key1: label-value1
  annotations:
    annotation-key1: annotation-value
    annotation-key2: long annotation value
spec:
  containers:
    - name: container-name
      image: image-name
      ports:
        - containerPort: 80
      livenessProbe:
        httpGet:
          path: /health
          port: 80
        initialDelaySeconds: 0
        periodSeconds: 10
        timeoutSeconds: 1
        successThreshold: 1
        failureThreshold: 3
      readinessProbe:
        httpGet:
          path: /
          port: 80
        initialDelaySeconds: 0
        periodSeconds: 10
        timeoutSeconds: 1
        successThreshold: 1
        failureThreshold: 3
      startupProbe:
        httpGet:
          path: / 
          port: 80
        initialDelaySeconds: 0
        periodSeconds: 10
        timeoutSeconds: 1
        successThreshold: 1
        failureThreshold: 3
```

Ini sedikit penjelasan : 
- **initialDelaySeconds**, waktu setelah container jalan dan dilakukan pengecekan, default nya 0. Jika tak paham, bahasa gampangnya pertama kali ketika mau pengecekan setelah berapa detik.
- **periodSeconds**, seberapa sering pengecekan dilakukan, default nya 10, misal diatur 10 detik, maka akan dicek setiap 10 detik sekali.
- **timeoutSeconds**, waktu timeout ketika pengecekan gagal, default 1, bahasa gampangnya jika waktu respon yang dibutuhkan lebih dari interval waktu yang ditentukan akan dianggap tidak sehat.
- **successThreshold**, minimum dianggap sukses setelah berstatus failure, default 1, jika disaat pengecekan belum melebihi angka yang di tentukan, maka belum dianggap sehat.
- **failureThreshold**, minimum dianggap gagal, default 3, jika disaat pengecekan tidak sehat melebihi angka yang di tentukan, maka dianggap tidak sehat.

Ini salah satu contoh jika benar dari Liveness Probe (tidak akan perulangan) :
5.1.3.2-nginx-pod-probe.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-with-probe
spec:
  containers:
    - name: nginx
      image: nginx
      ports:
        - containerPort: 80
      livenessProbe:
        httpGet:
          path: /
          port: 80
        initialDelaySeconds: 5
        periodSeconds: 5
        timeoutSeconds: 1
        successThreshold: 1
        failureThreshold: 3
```
Dan ini salah satu contoh yang terdapat kesalahan dari Liveness Probe (akan ada perulangan) : 
5.1.3.3-nginx-pod-falseprobe.yaml
```yaml 
apiVersion: v1
kind: Pod
metadata:
  name: nginx-with-falseprobe
spec:
  containers:
    - name: nginx
      image: nginx
      ports:
        - containerPort: 80
      livenessProbe:
        httpGet:
          path: /404
          port: 80
        initialDelaySeconds: 5 
        periodSeconds: 5
        timeoutSeconds: 1
        successThreshold: 1
        failureThreshold: 3
```
Untuk memasukkan seperti biasa menggunakan : 

```
$ kubectl create -f (namafile.yaml)
```

> *Pada (namafile.yaml) diganti sesuai dengan file yang dibuat*

Disini, sebagai pemahaman akan dilakukan 2 kali percobaan, Pod yang berhasil dan Pod yang gagal.

Pada Pod yang berhasil : 
```bash
$ kubectl create -f 5.1.3.2-nginx-pod-probe.yaml
pod/nginx-with-probe created
$ kubectl get pod
NAME               READY   STATUS    RESTARTS   AGE
nginx-with-probe   1/1     Running   0          32s
```
Disini dilhat bahwa status restart masih 0 karena tidak ada error, maksudnya karena tidak eror gimana? Nahh sekraang kita buka deskripsi dari Pod tersebut :
```
$ kubectl describe pod nginx-with-probe
...
    Ready:          True
    Restart Count:  0
    Liveness:       http-get http://:80/ delay=5s timeout=1s period=5s #success=1 #failure=3
    Environment:    <none>
...
```
Disini, bisa kita lihat langsung pada bagian Liveness, yang sesuai dengan konfigurasi : 
- **http-get**, pengecekan metode dari respon http get apakah yang ditarget ada atau mengluarkan hasil yang sesuai (bukan not found atau apapun).
-  **http://:80/**, target pengecekan. apakah dari url tersebut mengeluarkan hasil yang sesuai.
-  **delay=5s timeout=1s period=5s #success=1 #failure=3**, status yang terjadi dan dikeluarkan berdasarkan konfigurasi yang sudah dibuat pada yaml.

Kasus ini seperti di gambar ini : 
![9 (2)](https://hackmd.io/_uploads/By03RpOi0.png)


Masih bingung ya? Tak apa, nanti kita akan bedakan dengan contoh satunya (yang gagal).

Pada Pod 5.1.3.2-nginx-pod-probe tidak akan bertambah restart atau status tersebut karena pengecekan berhasil terus dan tidak ada kendala saat Pod di jalankan.

Kenapa tak error karena kita hanya mempersiapkan di port 80 yang dimana nginx berhasil mengeluarkan keluaran (nginx disini, jika tanpa setting dia akan mengeluarkan file default bahwa nginx berjalan), dan file default tersebut ada otomatis, sehingga Probe pada Pod tidak  perlu menguklangi Pod.

Saatnya kita coba yaml yang satunya yang dimana, konfigurasi Probe mendengarkan dari direktori web server 404 (sedangkan kita sendiri di web tidak menyiapkan untuk 404, bahkan nginx sama sekali masih kosong).

Langsung eksekusi :
```bash
$ kubectl create -f 5.1.3.3-nginx-pod-falseprobe.yaml
pod/nginx-with-falseprobe created
$ kubectl get pod
NAME                    READY   STATUS    RESTARTS   AGE
nginx-with-falseprobe   1/1     Running   0          8s
```
Dan sebagai pembuktian bahwa kita tidak menyiapkan direktori 404, kita akan forward : 
```
$ kubectl port-forward nginx-with-falseprobe 8888:80
Forwarding from 127.0.0.1:8888 -> 80
Forwarding from [::1]:8888 -> 80
$ curl 
```
Cek di beda sesi SSH :
```
$ curl 127.0.0.1:8888/404
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx/1.27.1</center>
</body>
</html>
```
Lihat bahwa html pada 404 akan menampilkan notfound. Jika balik lagi ke SSH yang utama (perintah jalankkan Pod Error) dan kita jalan status Pod : 
```
$ kubectl get pod
NAME                    READY   STATUS             RESTARTS      AGE
nginx-with-falseprobe   0/1     CrashLoopBackOff   5 (23s ago)   3m14s
```
Status Pod error dan restart terus dilakukan. Semakin lama, jumlah mengulang akan bertambah terus. Lihat deskripsi Pod :
```
$ kubectl describe pod nginx-with-falseprobe
...
Events:
  Type     Reason     Age                  From               Message
  ----     ------     ----                 ----               -------
  Normal   Scheduled  12m                  default-scheduler  Successfully assigned default/nginx-with-falseprobe to minikube
  Normal   Pulled     12m                  kubelet            Successfully pulled image "nginx" in 4.517s (4.517s including waiting). Image size: 187694648 bytes.
  Normal   Pulled     11m                  kubelet            Successfully pulled image "nginx" in 4.216s (4.216s including waiting). Image size: 187694648 bytes.
  Normal   Created    11m (x3 over 12m)    kubelet            Created container nginx
  Normal   Started    11m (x3 over 12m)    kubelet            Started container nginx
  Normal   Pulled     11m                  kubelet            Successfully pulled image "nginx" in 4.161s (4.161s including waiting). Image size: 187694648 bytes.
  Normal   Pulling    11m (x4 over 12m)    kubelet            Pulling image "nginx"
  Warning  Unhealthy  11m (x9 over 12m)    kubelet            Liveness probe failed: HTTP probe failed with statuscode: 404
  Normal   Killing    11m (x3 over 11m)    kubelet            Container nginx failed liveness probe, will be restarted
  Warning  BackOff    2m4s (x40 over 10m)  kubelet            Back-off restarting failed container nginx in pod nginx-with-falseprobe_default(2305b757-b6a5-4c96-9b44-61f9ad06b225)
```

Disini yang perlu diperhatikan adalah bagian Event yang diman dilihat pada bagian akhir, Probe akan mengetahui bahwa keadaan tersebut tidak sesuai yang dimana 404 tidak ada dan akan mengulangi sistem Pod, yang diharap 404 bisa berjalan (walaupun sebenarnya pada contoh 404 tidak akan ada karena memang belum di buat). Seperti di gambar ini : 

![10 (2)](https://hackmd.io/_uploads/H1iA0auoR.png)


Kesimpulan yang ada dari di atas adalah, Probe bertugas mengecek dan akan terus memastilkan bahwa sstem Pod berjalan dengan melihat keluaran dari yang dituju.

Pada kasus real, ini digunakan ketika suatu layanan atau bagian atau bahkan seluruh error atau tidak merespon sesuai yang ada di konfigurasi Probe, dan Probe akan  membantu anda mengulangi Pod, sehingga layanan kembali sesuai teget dari Probe.

### 5.2 Replication Controller

Replication Controller bertugas untuk memastikan bahwa Pod selalu berjalan. Jika tiba-tiba Pod mati atau hilang, misal ketika ada Node yang mati. Maka Replication Controller secara otomatis akan menjalankan Pod yang mati atau hilang tersebut.

Replication Controller biasanya ditugaskan untuk memanage lebih dari 1 Pod Replication Controller akan memastikan jumlah Pod yang berjalan sejumlah yang telah ditentukan. Jika kurang, makan aman menambah Pod baru, jika lebih maka akan menghapus Pod yang sudah ada.

Sebagai contoh seperti skenario di bawah  : 

1. Replication Controller akan memasukkan dan memastikan Pod ada terus, pada kasus skenario di bawah Replication Controller menggunakan Node 1 untuk menyimpan Pod 1.

    ![9](https://hackmd.io/_uploads/SkcdISvsR.png)

2. Pada suatu kasus ternyata Node 1 memiliki gangguan sehingga Node 1 tidak online, maka Replication Controller akan menaruh Pod 1 yang sudah di atur di Node lain yang online secara otomatis.

    ![10](https://hackmd.io/_uploads/HkYbwrvoA.png)


#### 5.2.1 Isi Replication Controller

- Label Selector, sebagai penanda Pod.
- Replica Count, jumlah Pod yang seharusnya berjalan.
- Pod Template, template yang digunakan untuk menjalankan Pod berisi konfigurasi untuk Pod.

#### 5.2.2 Membuat Replication Controller

Untuk membuat Replication Controller gunakan tempalate yang ada di bawah ini : 
5.2.2.1-template-replicationcontroller.yaml
```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: nama-replication-controller
  labels:
    label-key1: label-value1 
  annotations:
    annotation-key1: annotation-value1
spec:
  replicas: 3
  selector:
    label-key1: label-value1
  template:
    metadata:
      name: nama-pod
      labels:
        label-key1: label-value1
    spec:
      containers:
        - name: container-name
          image: image-name
          ports:
            - containerPort: 80
          readinessProbe:
            httpGet:
              path: /health
              port: 80
            initialDelaySeconds: 0
            periodSeconds: 10
            failureThreshold: 3
            successThreshold: 1
            timeoutSeconds: 1
```
Untuk contoh kita gunakan : 
5.2.2.2-3x-nginx-replicationcontroller.yaml
```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx-rc
spec:
  replicas: 3
  selector:
    app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
```
Untuk menjalankan dari Replication Controller menggunakan perintah ini : 

```
$ kubectl create -f (file.yaml)
```
> *Untuk (file.yaml) disesuaikan dengan nama file yaml yang dibuat tanpa ada tanda kurung*

Contoh jika yang di ambil dari
5.2.2.2-3x-nginx-replicationcontroller.yaml
```
$ kubectl create -f 5.2.2.2-3x-nginx-replicationcontroller.yaml
```
Sehingga akan mengluarkan keluaran seperti ini : 
```
replicationcontroller/nginx-rc created
```

#### 5.2.3 Melihat Replication Controller

Mirip-mirip seperti sebelumnya, ini untuk melihat Replication Controller : 

```
$ kubectl get replicationcontrollers
```
or 
```
$ kubectl get replicationcontroller
```
or
```
$ kubect1 get re
```
It will outputed like this : 
```
$ kubectl get replicationcontrollers
NAME       DESIRED   CURRENT   READY   AGE
nginx-rc   3         3         3       16s
```
Dapat dilihat disini, bahwa Replication Controller sudah berjalan dan menambah Pod yang sesuai, ini buktinya : 

```
$ kubectl get pod
NAME             READY   STATUS    RESTARTS   AGE
nginx-rc-djrc6   1/1     Running   0          106s
nginx-rc-j54n6   1/1     Running   0          106s
nginx-rc-jnz58   1/1     Running   0          106s
```
> *Pada Replication Controller, tak perlu khawaitr karena Pod yang dibuat pasti akan berbeda nama pada akhir nama Pod, jadi tenang saja*

Setelah ini akan mencoba apakah efek Replication Controller akan berjalan.

#### 5.2.4 Percobaan Jika Salah Satu Pod di Hapus

Oke, disini masih menggunakan Replication Controller dan Pod yang masih sama : 
```
$ kubectl get pod
NAME             READY   STATUS    RESTARTS   AGE
nginx-rc-djrc6   1/1     Running   0          106s
nginx-rc-j54n6   1/1     Running   0          106s
nginx-rc-jnz58   1/1     Running   0          106s
```
Terlihat jelas dan ingat-ingat dari identifier masing-masing Pod. Misal disini akan dihapus Pod `nginx-rc-jnz58` : 
```
$ kubectl delete pod nginx-rc-jnz58
pod "nginx-rc-jnz58" deleted
```
Nah, disini sudah di hapus manual untuk pod `nginx-rc-jnz58`. Tapi kita akan cek, lagi : 
```
$ kubectl get pod
NAME             READY   STATUS    RESTARTS   AGE
nginx-rc-djrc6   1/1     Running   0          12m
nginx-rc-j54n6   1/1     Running   0          12m
nginx-rc-q7mc7   1/1     Running   0          108s
```

Lihat, Replication Controller otomatis akan membuat ulang. Tak Percaya? Lihat id yang baru dan lihat juga durasi Umur dari Pod.
Percaya kan? Itulah kerja dari Replication Controller.

#### 5.2.5 Menghapus Replication Controller

Saat kita menghapus Replication Controller, maka secara otomatis Pod yang berada pada label selectornya akan ikut terhapus.Jika kita ingin menghapus Replication Controller, tanpa menghapus Pod yang berada pada labelselectornya, kita bisa tambahkan opsi `--cascade=false`

Cara menghapus Repilcation Controller tanpa Pod yang ada, adalah : 
```bash
$ kubectl delete rc (rcname)
```
Untuk menghapus dengan Pod yang ada gunakan : 
```bash
$ kubectl delete rc (rcname) --cascade=false
```

Disini ingin dihapus biasa untuk Replication Controller, maka menggunakan perintahnya : 
```bash
$ kubectl delete rc nginx-rc
replicationcontroller "nginx-rc" deleted
```
Disini Replication Controller sudah terhapus, tetapi jika dilihat Pod yang sebelumnya di buat : 
```bvsh
$ kubectl get pod
NAME             READY   STATUS    RESTARTS      AGE
nginx-rc-djrc6   1/1     Running   1 (10h ago)   10h
nginx-rc-j54n6   1/1     Running   1 (10h ago)   10h
nginx-rc-q7mc7   1/1     Running   1 (10h ago)   10h
```
Pod yang dibuat oleh Replication Controller masih ada, karena metode hapus yang biasa. Demikian untuk menghapus Replication Controller.

### 5.3 Replica Set

#### 5.3.1 Penjelasan Replica Set

Replica Set merupakan pengembangan dari Replication Controller. Secara kinerja,  masih sama seperti Replication Controller tetapi terdapat tambahan label selector yang lebih expressive dibandingkan Replication Controller yang hanya memiliki fitur label selector secara match. Sehingga, Replication Controller sekarang tidak disarankan penggunannya.

Berikut template untuk membuat Replica Set : 
5.3.1.1-template-replicaset.yaml
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replica-set-name
  labels:
    label-key1: label-value1
  annotations:
    annotation-key1: annotation-value1
spec:
  replicas: 3
  selector:
    matchLabels:
      label-key1: label-value1
  template:
    metadata:
      name: pod-name
      labels:
        label-key1: label-value1
    spec:
      containers:
      - name: container-name
        image: image-name
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /health
            port: 80
          initialDelaySeconds: 0
          periodSeconds: 10
          failureThreshold: 3
          successThreshold: 1
          timeoutSeconds: 1
```
Perlu diperhatikan : 
- **apiVersion: apps/v1** ; pastikan untuk replica set gunakan apps dikarenakan memerlukan versi terbaru.
- **selector : match-labels** ; untuk menyamakan key-value dengan template, seperti di Replication Controller.

Untuk penjelasan lanjutnya akan dijelaskan subbab setelah ini.

Akan dicoba contoh :
5.3.1.2-3x-nginx-replicaset.yaml
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
```

Untuk menjalankan sama seperti sebelumnya : 
```
$ kubectl create -f (file.yaml)
```
> *Untuk (file.yaml) disesuaikan dengan nama file yaml yang dibuat tanpa ada tanda kurung

Dan untuk melihat list Replica Set, gunakan :

```
$ kubectl get rs
```

Sebagai contoh kita akan jalankan 5.3.1.2-3x-nginx-replicaset.yaml : 
```
$ kubectl create -f 5.3.1.2-3x-nginx-replicaset.yaml
replicaset.apps/nginx created
$ kubectl get pod
NAME          READY   STATUS    RESTARTS   AGE
nginx-8t2ng   1/1     Running   0          20s
nginx-dr8vg   1/1     Running   0          20s
nginx-pmxxk   1/1     Running   0          20s
$ kubectl get rs
NAME    DESIRED   CURRENT   READY   AGE
nginx   3         3         3       39s
```

Lihat, untuk penggunaan dan cara pada Replica Set sangatlah mirip dengan Replication Controller, hanya beda sedikit sahaja.

#### 5.3.2 Label Selector Match Expression

Sebelumnya, jika diperhatikan, untuk selector di Replication Set kita menggunakan `matchLabels`, yang artinya selector tersebut cara kerjanya match (sama seperti di Replication Controller). Tetapi, selain menggunakan `matchLabels`, operasi lain yang bisa digunakan pada selector di ReplicationSet adalah menggunakan `matchExpression`.

Operasi di Match Expression : 
- **In**, value label harus ada di value in.
- **NotIn**, value tabel tidak boleh ada di value in.
- **Exists**, label harus ada.
- **NotExists**, label tidak boleh ada.

Template seperti di bawah ini : 
5.3.2.1-template-replicasetwith labelselector.yaml
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replica-set-name
  labels:
    label-key1: label-value1
  annotations:
    annotation-key1: annotation-value1
spec:
  replicas: 3
  selector:
    matchLabels:
      label-key1: label-value1
    matchExpressions:
      - key: label-key
        operator: In
        values:
          - label-value1
          - label-value2
  template:
    metadata:
      name: pod-name
      labels:
        label-key1: label-value1
    spec:
      containers:
      - name: container-name
        image: image-name
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /health
            port: 80
          initialDelaySeconds: 0
          periodSeconds: 10
          failureThreshold: 3
          successThreshold: 1
          timeoutSeconds: 1
```
Contoh seperti ini : 
5.3.2.2-3x-nginx-replicasetlabelselector.yaml
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchExpressions:
      - key: app
        operator: In
        values:
          - nginx
      - key: env
        operator: In
        values:
          - prod
          - qa
          - dev
  template:
    metadata:
      name: nginx
      labels:
        app: nginx
        env: prod
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
```
Dan sekarang akan dicoba : 
```
$ kubectl create -f 5.3.2.2-3x-nginx-replicasetlabelselector.yaml
replicaset.apps/nginx created
$ kubectl get rs
NAME    DESIRED   CURRENT   READY   AGE
nginx   3         3         3       23s
$ kubectl get pod
NAME          READY   STATUS    RESTARTS   AGE
nginx-c58nw   1/1     Running   0          32s
nginx-lmsnv   1/1     Running   0          32s
nginx-s92jn   1/1     Running   0          32s
kubectl get pod --show-labels
NAME          READY   STATUS    RESTARTS   AGE     LABELS
nginx-c58nw   1/1     Running   0          44s   app=nginx,env=prod
nginx-lmsnv   1/1     Running   0          44s   app=nginx,env=prod
nginx-s92jn   1/1     Running   0          44s   app=nginx,env=prod
```

Nah, jika dilihat dalam label, Pod memiliki masing-masing label env yang ada, jadi sistem memastikan label berada dalam label yang sudah ditentukan.

