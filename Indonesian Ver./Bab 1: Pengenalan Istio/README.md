## Bab 1 : Pengenalan Istio

![Istio](https://hackmd.io/_uploads/rkECKE7dke.png)

### 1.1 Konsep Dasar Service Mesh

Service Mesh adalah arsitektur jaringan yang dirancang untuk menangani komunikasi antar layanan (service-to-service) dalam lingkungan microservices. 

Dalam lingkungan seperti ini, jumlah layanan dapat sangat banyak, dan komunikasi antar layanan bisa menjadi kompleks. Service Mesh memberikan solusi untuk mengelola komunikasi ini secara lebih terstruktur, aman, dan andal.

Fungsi Utama Service Mesh :
1. Observability (Pengamatan) : Memantau lalu lintas jaringan antar layanan, termasuk latensi, error rate, dan metrik lainnya.
2. Traffic Management (Manajemen Lalu Lintas): Mengontrol bagaimana permintaan (request) dialirkan di antara layanan, termasuk load balancing, routing, dan rate limiting.
3. Security (Keamanan): Mengamankan komunikasi antar layanan melalui enkripsi, autentikasi, dan otorisasi.
4. Policy Enforcement (Penerapan Kebijakan): Mengimplementasikan kebijakan tertentu seperti pembatasan akses dan penanganan kuota.

Komponen dalam Service Mesh:
1. Data Plane : Mengelola lalu lintas data secara langsung antar layanan. Biasanya berupa proxy sidecar seperti Envoy.
2. Control Plane: Bertanggung jawab untuk mengatur dan mengelola konfigurasi Data Plane.

### 1.2 Apa itu Istio?

Menurut Google Cloud, Istio adalah mesh layanan (service mesh), yakni lapisan jaringan layanan modern yang menyediakan cara yang transparan dan tidak bergantung pada bahasa untuk mengotomatiskan fungsi jaringan aplikasi secara fleksibel dan mudah. Solusi ini populer untuk mengelola berbagai microservice yang membentuk aplikasi berbasis cloud. Mesh layanan Istio juga mendukung cara microservice tersebut berkomunikasi dan berbagi data satu sama lain.

Fitur-fitur Istio yang canggih menyediakan cara yang seragam dan lebih efisien untuk mengamankan, menghubungkan, dan memantau layanan. Istio adalah jalur menuju penyeimbangan beban (load balancing), autentikasi antar-layanan, dan pemantauan (dengan sedikit atau tanpa perubahan kode layanan).

Fitur-fitur yang ada di Istio adalah : 

- Mengamankan komunikasi antar layanan dalam sebuah cluster dengan enksripsi TLS secara bersama, autentikasi dan otorisasi berbasis identitas yang kuat.
- Penyeimbangan beban otomatis untuk lalu lintas HTTP, gRPC, WebSocket, dan TCP.
- Kontrol perilaku lalu lintas yang terperinci dengan aturan perutean yang lengkap, percobaan ulang, failover, dan injeksi kesalahan.
- Lapisan kebijakan yang dapat dipasang dan API konfigurasi yang mendukung kontrol akses, batas kecepatan, dan kuota.
- Metrik, log, dan jejak otomatis untuk semua lalu lintas dalam kluster, termasuk masuk dan keluar kluster.

### 1.3 Kapan Istio di gunakan?

1. Aplikasi Berbasis Mikroservis yang Kompleks : Ketika memiliki banyak mikroservis yang saling berkomunikasi, Istio dapat membantu mengelola lalu lintas, keamanan, dan observability secara terpusat.
2. Kebutuhan Keamanan yang Tinggi : Jika  mengenkripsi semua komunikasi antara mikroservis dan memerlukan mekanisme autentikasi dan otorisasi yang canggih.
3. Observability yang Mendalam : Ketika perlu memonitor dan melacak permintaan melintasi berbagai mikroservis dengan tools seperti Prometheus, Grafana, Jaeger, dan Zipkin.
4. Resilience dan Fault Tolerance : Jika perlu meningkatkan ketahanan aplikasi terhadap kegagalan dengan fitur seperti retry, timeout, dan circuit breaking.
5. Kontrol Kebijakan yang Fleksibel: Ketika perlu membatasi jumlah permintaan atau mengelola kuota penggunaan sumber daya.
6. Integrasi dengan Kubernetes: Jika menggunakan Kubernetes untuk mengelola container, Istio dirancang untuk bekerja secara native dengan Kubernetes.
7. Kebutuhan untuk Canary Deployment dan A/B Testing: Jika perlu melakukan canary deployment atau A/B testing untuk menguji versi baru aplikasi secara bertahap 

### 1.4 Mengapa Menggunakan Istio?

1. Mencapai jaringan layanan yang konsisten
Operator jaringan dapat mengelola jaringan di semua layanannya secara konsisten tanpa perlu menambah beban developer.
2. Mengamankan layanan dengan manfaat Istio 
Operator keamanan dapat dengan mudah menerapkan keamanan layanan-ke-layanan, termasuk autentikasi, otorisasi, dan enkripsi.
3. Meningkatkan performa aplikasi
Terapkan praktik terbaik, seperti peluncuran canary, dan dapatkan visibilitas mendalam ke aplikasi untuk mengidentifikasi area fokus guna meningkatkan performa.

### 1.5 Komponen Utama Istio

#### 1.5.1 Envoy Proxy

Proxy berbasis sidecar yang berfungsi sebagai data plane untuk mengelola lalu lintas antar layanan. Envoy menangani routing, load balancing, enkripsi (mTLS), dan observabilitas.

#### 1.5.2 Istiod (Control Plane)

1. Pilot

Mengatur dan mengelola konfigurasi routing untuk proxy Envoy. Pilot di Istio juga Menyediakan mekanisme service discovery sehingga layanan dapat saling menemukan, dan Memberikan konfigurasi trafik seperti load balancing, failover, dan routing ke proxy sidecar.

Contoh penggunaan : Mengatur versi layanan tertentu yang hanya digunakan untuk pengujian (canary deployment).

2. Mixer

Berfungsi menyediakan fitur observability, policy enforcement, dan telemetri. Mixer juga berperan mengumpulkan metrik, log, dan jejak (tracing) dari lalu lintas layanan, juga Menegakkan kebijakan seperti pembatasan kuota dan autentikasi.

3. Citadel

Citadel merupakan bagian yang menyediakan keamanan dengan fitur seperti autentikasi dan enkripsi. Citadel juga mengelola sertifikat TLS/SSL untuk memastikan komunikasi antar layanan terenkripsi.

Contoh Penggunaan: Semua komunikasi antar layanan berjalan menggunakan protokol HTTPS yang terenkripsi.

#### 1.5.3 Gateway

Istio Gateway memungkinkan pengelolaan lalu lintas masuk (ingress) dan keluar (egress) dari mesh. Gateway juga membantu mengamankan dan mengatur akses ke layanan dari luar.

#### 1.5.4 Operator

Digunakan untuk mempermudah instalasi dan pengelolaan Istio di cluster Kubernetes. Mengotomatiskan proses deployment dan pembaruan.
