## Bab 3 : Arsitektur Istio

![Istio (2)](https://hackmd.io/_uploads/S14NArmOye.png)

### 3.1 Data Plane : Proxy Sidecar dengan Envoy

Envoy merupakan sebuah proksi berperforma tinggi di kembangkan dengan bahasa C++ untuk menengahi semua lalu lintas masuk dan keluar untuk semua layanan dalam jaringan. Proksi Envoy adalah satu-satunya komponen Istio yang berinteraksi dengan lalu lintas data plane.

Envoy proxy digunakan sebagai sidecar untuk layanan, menambahkan berbagai fitur bawaan Envoy seperti:

- Penemuan layanan dinamis
- Load balancing
- Terminasi TLS
- Proxy HTTP/2 dan gRPC
- Circuit breakers
- Pemeriksaan kesehatan
- Rollout bertahap dengan pembagian traffic berbasis persentase
- Suntikan kesalahan (fault injection)
- Metrik yang kaya

Penerapan sidecar ini memungkinkan Istio untuk menegakkan keputusan kebijakan dan mengekstrak telemetri lengkap yang dapat dikirim ke sistem pemantauan untuk memberikan informasi tentang perilaku seluruh jaringan.

Model proxy sidecar juga memungkinkan untuk menambahkan kapabilitas Istio ke penerapan yang sudah ada tanpa mengharuskan untuk merancang ulang atau menulis ulang kode.

Beberapa fitur dan tugas Istio yang didukung oleh Envoy proxy meliputi :

- Kontrol lalu lintas : aturan routing detail untuk HTTP, gRPC, WebSocket, dan TCP.
- Ketahanan jaringan : retry, failover, circuit breakers, dan fault injection.
- Keamanan dan autentikasi : penerapan kebijakan keamanan, kontrol akses, dan pembatasan kecepatan.
- Model ekstensi berbasis WebAssembly : memungkinkan kebijakan kustom dan pembuatan telemetri untuk lalu lintas mesh.

### 3.2 Control Plane : Peran Istiod

Istiod menyediakan penemuan layanan, konfigurasi, dan manajemen sertifikat. Ia mengonversi aturan perutean tingkat tinggi ke konfigurasi Envoy dan menyebarkannya ke sidecar saat runtime. Istiod mengabstraksi penemuan layanan dari berbagai lingkungan (seperti Kubernetes atau VM) ke format standar yang kompatibel dengan Envoy.

Juga dapat menggunakan API Manajemen Lalu Lintas Istio untuk mengontrol lalu lintas secara lebih terperinci. Istiod juga memungkinkan autentikasi layanan-ke-layanan dan pengguna akhir yang kuat, serta enkripsi lalu lintas dengan mTLS. Operator dapat memberlakukan kebijakan keamanan berdasarkan identitas layanan, bukan pengenal jaringan, dan menggunakan fitur otorisasi untuk mengontrol akses ke layanan.

Sebagai Otoritas Sertifikat (CA), Istiod menghasilkan sertifikat untuk memastikan komunikasi aman di bidang data.

### 3.3 Service Discovery dan Konfigurasi Dynamic

Dalam arsitektur Istio, Istiod berperan penting dalam menyediakan service discovery, konfigurasi, dan manajemen sertifikat. Istiod mengabstraksi mekanisme service discovery spesifik platform dan menyintesisnya ke dalam format standar yang dapat dikonsumsi oleh sidecar seperti Envoy. Ini memungkinkan Istio mendukung discovery untuk berbagai lingkungan seperti Kubernetes atau mesin virtual.

Untuk layanan yang berjalan di luar Kubernetes atau menggunakan registry layanan pihak ketiga seperti Consul atau Eureka, Istio memungkinkan integrasi melalui sumber data eksternal. Dengan menggunakan resource ServiceEntry, pengguna dapat secara manual menambahkan layanan eksternal ke dalam registry layanan Istio, memungkinkan Envoy mengarahkan lalu lintas ke layanan tersebut seolah-olah mereka adalah bagian dari mesh. 

### 3.4 Istio Gateway dan Virtual Service

Gateway dan VirtualService adalah komponen kunci dalam manajemen lalu lintas di Istio.

- Gateway : Gateway mengonfigurasi load balancer untuk mengelola lalu lintas masuk atau keluar dari mesh. Ini menentukan port yang akan diekspos, protokol yang digunakan, dan pengaturan TLS. Gateway berfungsi sebagai titik masuk untuk lalu lintas HTTP/TCP ke dalam mesh. 
- VirtualService : VirtualService mendefinisikan seperangkat aturan perutean lalu lintas yang diterapkan saat host tertentu diakses. Setiap aturan perutean menentukan kriteria pencocokan untuk lalu lintas dengan protokol tertentu. Jika lalu lintas sesuai dengan kriteria tersebut, maka akan diarahkan ke layanan tujuan yang ditentukan dalam registry. VirtualService memungkinkan kontrol perutean yang lebih granular, seperti implementasi skenario canary release atau A/B testing. 

Dengan menggabungkan Gateway dan VirtualService, dapat mengontrol bagaimana lalu lintas dari luar mesh diarahkan ke layanan internal, serta mengelola perutean lalu lintas di dalam mesh itu sendiri.
