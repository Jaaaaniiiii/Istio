## Bab 2 : Instalasi Istio

### 2.1 Persyaratan Sistem

Cluster Kubernetes 1.10+ dengan kontrol akses berbasis peran (RBAC) diaktifkan. Direkomendasikan cluster dengan setidaknya 8GB memori yang tersedia dan 4vCPU.

Atau dapat melkukan install pada :
- Minikube
- Docker Desktop
- kind
- Microk8s

Dengan juga terdapat Kubernetes CLI (kubectol).

### 2.2 Macam Profil Instalasi Istio

Profil instalasi Istio dapat digunakan untuk menyesuaikan deployment Istio sesuai kebutuhan. Setiap profil memiliki tujuan dan komponen yang berbeda. Berikut penjelasan yang lebih baik untuk setiap profil :

1. Default Profile

a. Tujuan : Untuk deployment produksi atau cluster utama dalam skenario multi-cluster.
b. Komponen yang Di-deploy :
   - Control Plane : Komponen inti Istio yang mengelola konfigurasi dan kebijakan.
   - Ingress Gateway : Gateway untuk mengelola lalu lintas masuk (inbound traffic) ke cluster.
c. Kegunaan : Cocok untuk lingkungan produksi yang membutuhkan ketersediaan tinggi dan keamanan.

2. Demo Profile

a. Tujuan : Untuk demonstrasi atau pengujian.
b. Komponen yang Di-deploy :
   - Control Plane.
   - Ingress Gateway (untuk lalu lintas masuk).
   - Egress Gateway (untuk lalu lintas keluar).
c. Tracing dan Access Logging : Tingkat logging yang tinggi untuk memudahkan debugging dan observabilitas.
d. Kegunaan : Ideal untuk presentasi, pengujian fitur, atau pembelajaran.

3. Minimal Profile

a. Tujuan : Untuk deployment ringan tanpa gateway.
b. Komponen yang Di-deploy hanya Control Plane.
c. Kegunaan : Cocok untuk skenario di mana gateway tidak diperlukan, seperti lingkungan pengembangan atau testing.

4. External Profile

a. Tujuan : Untuk mengkonfigurasi cluster remote dalam skenario multi-cluster.
b. Tidak ada komponen yang di-deploy.
c. Kegunaan : Digunakan untuk menghubungkan cluster remote ke cluster utama yang sudah memiliki Istio terinstal.

5. Empty Profile

a. Tujuan : Sebagai dasar untuk konfigurasi kustom.
b. Tidak ada komponen yang di-deploy.
c. Kegunaan : Memberikan kebebasan penuh kepada pengguna untuk menambahkan komponen sesuai kebutuhan.

6. Preview Profile

a. Tujuan : Untuk mencoba fitur eksperimental.
b. Komponen yang Di-deploy : 
   - Control Plane.
   - Ingress Gateway.
c. Kegunaan : Cocok untuk pengguna yang ingin menguji fitur baru Istio yang belum stabil. Tidak disarankan untuk produksi.

7. Ambient Profile

a. Tujuan: Untuk memulai dengan Ambient Mesh, mode baru Istio yang masih dalam fase Alpha (per April 2024).
b. Komponen yang Di-deploy :
   - Control Plane.
   - Ingress Gateway.
c. Kegunaan : Dirancang untuk pengguna yang ingin bereksperimen dengan Ambient Mesh, yang menghilangkan kebutuhan sidecar proxy tradisional. Tidak disarankan untuk produksi.

Untuk mengsekplor tiap profile dapat di cek dengan command :

```bash
$ istioctl profile dump <profile-name>
```

> `<profile-name>` diisi sesuai profile yang mau di explore

Sehingga perintah di atas akan menampilkan YAML konfigurasi dari profile yang diisi, dan juga dapat membandingkan dua profil dengan perintah :

```bash
$ istioctl profile diff <proofile-name-1> <profile-name-2>
```
> `<proofile-name-1> <profile-name-2>` diubah sesuai dengan profile yang ingin dibandingkan

Sehinga akan muncul perbandingan dan perbedaan dua konfigurasi YAML dari dua profil, sebagai contoh :

```bash
$ istioctl profile diff demo default
```
Maka akan beroutput :
```yaml
The difference between profiles:
 apiVersion: install.istio.io/v1alpha1
 kind: IstioOperator
 metadata:
   creationTimestamp: null
   namespace: istio-system
 spec:
   components:
     base:
       enabled: true
     cni:
       enabled: false
     egressGateways:
-    - enabled: true
-      k8s:
-        resources:
-          requests:
-            cpu: 10m
-            memory: 40Mi
+    - enabled: false
       name: istio-egressgateway
     ingressGateways:
     - enabled: true
```

Outputnya dalam format diff tradisional, termasuk garis yang ditandai dengan + atau - untuk menunjukkan perbedaan antara dua profil.

### 2.3 Istio Operator API dan IstioOperator

Istio Operator API adalah mekanisme yang memungkinkan pengguna untuk mengelola instalasi dan konfigurasi Istio secara deklaratif. Sedangkan, IstioOperator Resource adalah file YAML yang mendefinisikan keadaan yang diinginkan (desired state) dari komponen-komponen Istio.

a. Struktur Konfigurasi IstioOperator

Bagian yang mengatur pengaturan gloval untuk instalasi istio, seperti Profil, Docker Imagw Path, Image Tags, dll.

b. Mesh Configuration (meshConfig)

Bagian yang mengatur kongfigurasi mesh (control plane), seperti Access Log Format, Log Encoding, Default Proxy Confioguration, dll.

c. Component Configruation (component)

Bagian yang mengatur komponen individual Istio, seperti Aktif/ Nonaktif Komponen, Resource Kubernetes (CPU/ Memory Request dan Limits), dan Instalasi Komponen Tambahan

Untuk menerapkan IstioOperator Resource dapat menggunakan perintah :

```bash
$ istioctl install -f <yaml-of-kuberntes-cluster>
```

> `<yaml-of-kuberntes-cluster>` harap di ubah sesuai dengan penamaan file atau lamat file/

### 2.4 Instalasi Istio di Kubernetes menggunakan IstioOperator (Default Way)

1. Memastikan ada Cluster yang aktif

```bash
$ kubectl cluster-info
```

2. Download Istio

Download rilis baru untuk Istio :
```bash
$ curl -L https://istio.io/downloadIstio | sh -
```
atau
```bash
$ curl -L ht‌tps://istio.io/downloadIstio | ISTIO_VERSION=1.21.0 sh -
```
Jika ingin mengkustom sebuah versi
```bash
$ cd istio-<version>
$ export PATH=$PWD/bin:$PATH
```
> `<version>` pastikan diubah sesuai nomer versi yang di ubah.

2. Verifikasi Koneksi ke Kluster Kubernetes

```bash
$ kubectl cluster-info
```

3. Instal Istio sesuai Versi dan profil yang dipih

```bash
$ istioctl install --set profile=<isto profile> -y
```
> `<istio profile>` pastikan diubah sesuai profile istio yang terjadi.

Disini sebagai saran dapat menggunakan profile demo : 

```bash
$ istioctl install --set profile=demo -y
```
Atau 
```bash
$ istioctl install -f demo-profile.yaml
```
Jika ingin/ memiliki profil kustom, dengan isi 'demo-profile.yaml' yaitu : 
```yaml
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
metadata:
  namespace: istio-system
  name: demo-installation
spec:
  profile: demo
```

4. Verifikasi Instalasi Istio Control Panel

```bash
$ kubectl get pods -n istio-system
``` 

### 2.5 Menginjeksi sidecar secara otomatis

1. Mengaktifkan Injeksi sidecar otomatis

Untuk mengaktifkan injeksi otomatis di sebuah namespace, jalankan perintah :

```bash
$ kubectl label namespace <namespace> istio-injection=enabled
```
> `<namespace>` pastikan di rubah dengan nammespace yang ada di kubernetes

Ini akan memberi label pada namespace tersebut sehingga Istio secara otomatis menyuntikkan sidecar proxy ke setiap Pod yang dibuat di namespace tersebut.

2. Mmeverifikasi injeksi sidecar

```bash
$ kubectl get namespace -L istio-injection
```

3. Membuat Deployment dan Memverifikasi Injeksi (dengan Nginx)

```bash
$ kubectl create deploy my-nginx --image=nginx
```

4. Periksa Pod yang dibuat 

```bash
$ kubectl get po
```

Output akan menunjukkan bahwa Pod memiliki 2 container (misalnya, 2/2 di kolom READY):

- Container 1 : nginx (aplikasi utama).
- Container 2 : istio-proxy (sidecar proxy yang disuntikkan oleh Istio).

```bash
$ kubectl describe po <pod-name>
```

> `<pod-name>` diubah sesuai yang ada di sistem

Output akan menampilkan informasi tentang kedua container (nginx dan istio-proxy) serta event yang terjadi selama pembuatan Pod.

5. Membersihkan Deployment

```bash
$ kubectl delete deployment <deployment-name>
```

> '<dpyolment-name>' pastikan ada sesuai yang ada di sistem .

### 2.6 Memperbarui Instalasi Istio dan Menghapus Instalasi Istio dengan IstioOperator

Untuk memperbarui instalasi Istio, Developer dapat memodifikasi resource IstioOperator yang sudah ada dan menerapkannya kembali ke cluster.
Contoh : Jika ingin menghapus egress gateway, developer dapat mengubah file YAML IstioOperator seperti ini :

```yaml
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
metadata:
  namespace: istio-system
  name: demo-istio-install
spec:
  profile: demo
  components:
    egressGateways:
    - name: istio-egressgateway
      enabled: false
```

Simpan file ini sebagai iop-egress.yaml dan terapkan menggunakan perintah:

```bash
$ istioctl install -f iop-egress.yaml
```

Setelah diterapkan, egress gateway akan dihapus dari cluster.

Disini juga bisa membuat resource IstioOperator terpisah untuk mengelola komponen tertentu secara independen

Contoh : Membuat resource untuk internal ingress gateway (hanya berlaku untuk cluster GCP)

```bash
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
metadata:
  name: internal-gateway-only
  namespace: istio-system
spec:
  profile: empty
  components:
    ingressGateways:
      - namespace: some-namespace
        name: ilb-gateway
        enabled: true
        k8s:
          serviceAnnotations:
            networking.gke.io/load-balancer-type: "Internal"
```

Untuk menghapus instalasi Istio sepenuhnya, gunakan perintah :

```
$ istioctl uninstall --purge
```

Perintah ini akan menghapus semua komponen Istio dari cluster.

### 2.7 Instalasi Istio dengan Helm

Sebelumnya, mungkin pemaknaan, apa itu Helm? Helm merupakan aplikasi open source package manager untuk Kubernetes yang memudah instalasi dan pembatuan aplikasi kompleks.

Dan di Helm, juga terdapat Helm Chart yang dimana merupakan kumpulan file YAML yang mendefinisikan aplikasi atau komponen yang akan di-deploy di Kubernetes.

Maka dari itu, ada tiga Helm Charts untuk istio :

- Base Chart (istio/base), resource yang bersifat cluster-wide 
- Istiod Chart (istio/istiod), komponen control plane Istio
- Gateway Chart (istio/gateway), resource untuk ingress dan egress gateway

Untuk menginstall Istio menggunakan Helm, dapat melakukan perintah di bawah ini :

Langkah 1: Buat namespace root (misalnya, istio-system) secara manual.

```bash
$ kubectl create namespace istio-system
```

Langkah 2: Instal chart base dan istiod ke namespace istio-system.

```bash
$ helm install istio-base istio/base -n istio-system
$ helm install istiod istio/istiod -n istio-system
```

Langkah 3: Instal chart gateway ke namespace yang sesuai (misalnya, istio-ingress).

```bash
$ kubectl create namespace istio-ingress
$ helm install istio-gateway istio/gateway -n istio-ingress
```

Langkah 4 : Memeriksa Status Instalasi Istio

```bash
$ helm status istiod -n istio-system
```

Dengan menginstall melalui Helm, dapat di ubah konfigurasi Helm Charts nya, dengan cara :

1. Meninjau Konfigurasi yang Tersedia

```bash
$ helm show values istio/istiod
```
Perintah ini akan menampilkan semua nilai default dan opsi konfigurasi yang dapat diubah untuk chart istio/istiod.

2. Contoh konfigurasi yang dapat diubah

Berikut contoh konfigurasi yang dapat di ubah :

```yaml
pilot:
  autoscaleEnabled: true  # Mengaktifkan autoscaling.
  autoscaleMin: 1         # Jumlah minimum replika.
  autoscaleMax: 5         # Jumlah maksimum replika.
  replicaCount: 1         # Jumlah replika default.
  rollingMaxSurge: 100%   # Jumlah maksimum Pod yang dapat ditambahkan selama rolling update.
  rollingMaxUnavailable: 25%  # Jumlah maksimum Pod yang tidak tersedia selama rolling update.
  hub: ""                 # Repository Docker untuk image.
  tag: ""                 # Tag Docker untuk image.
  image: pilot            # Nama image.
  traceSampling: 1.0      # Tingkat sampling untuk tracing (1.0 = 100%).
  resources:
    requests:
      cpu: 500m           # Request CPU.
      memory: 2048Mi      # Request memori.
  env: {}                 # Variabel lingkungan tambahan.
  cpu:
    targetAverageUtilization: 80  # Target utilisasi CPU untuk autoscaling.
```

3. Menerapkan Konfigurasi Kustom

Untuk melakukan kongiruasi kustom buatlah sebuah file YAML (misal cmy-config-values.yaml) yang berisi nlai yang ingin di ubah :

```yaml
pilot:
  replicaCount: 2
  resources:
    requests:
      cpu: 1000m
      memory: 4096Mi
```

Setelah selesai mengubah, gunakan perintah 'helm install' atau 'helm upgrade' dengan opsi '-f' :

```bash
$ helm install istiod istio/istiod -n istio-system -f my-config-values.yaml
```
atau 
```bash
helm upgrade istiod istio/istiod -n istio-system -f my-config-values.yaml
```

Dan jika butuh untuk menguninstall dapat menggunakan perintah :

```bash 
$ helm delete istiod  -n istio-system
```
