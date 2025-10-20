# Docker Standalone, Compose, dan Orchestration

### Docker Standalone
Docker Standalone adalah metode menjalankan container secara langsung menggunakan perintah dari Docker CLI. Metode ini cocok untuk menjalankan satu atau beberapa container sederhana tanpa konfigurasi kompleks. Contoh penggunaannya adalah menjalankan container dengan perintah seperti docker run.

### Docker Compose
Docker Compose adalah alat yang digunakan untuk mendefinisikan dan menjalankan aplikasi multi-container. Konfigurasi aplikasi dituliskan dalam sebuah file YAML (docker-compose.yml) yang berisi semua definisi service, termasuk environment variables, port forwarding dari host ke container, volume, dan network yang digunakan oleh container.

Dengan compose, pengguna dapat menjalankan semua layanan sekaligus dengan satu perintah seperti docker-compose up, sehingga proses deployment lebih efisien dibandingkan menjalankan container satu per satu.
### Docker Orchestration
Docker orchestration adalah proses otomatisasi deployment, manajemen, scaling, dan networking dari container yang terdiri dari beberapa node. Orkestrasi memungkinkan aplikasi berjalan secara terdistribusi dan otomatis diskalakan sesuai kebutuhan.

Salah satu tool orkestrasi paling populer adalah Kubernetes, yang didukung oleh banyak cloud provider besar seperti AWS, GCP, dan Azure. Untuk kebutuhan yang lebih ringan, tersedia alternatif seperti k3s, versi ringan dari Kubernetes yang dirancang untuk perangkat dengan resource terbatas, seperti Raspberry Pi. 
