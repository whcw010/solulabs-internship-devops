# Visualisasi Log Nginx Menggunakan Grafana yang Berjalan Secara Native
Grafana adalah tools opensource untuk menampilkan visualisasi data time-series. 

Contoh penggunaan Grafana adalah untuk memvisualisasikan sejarah status koneksi nginx untuk mengetahui jumlah total koneksi yang dilayani pada jangka waktu tertentu.
Berikut adalah langkah-langkah untuk distro Debian

### 1. Expose halaman stub_status nginx melalui subpath tertentu

Nginx menyediakan modul stub_status untuk menampilkan status dari koneksi yang diterima oleh nginx. Untuk mengakses halaman tersebut, edit konfigurasi nginx untuk menyajikan modul tersebut pada sub path tertentu.


```nginx
# /etc/nginx/conf.d/status.conf(pastikan file ini diinclude pada file nginx.conf)
server {
    listen 80;
    # Optionally: allow access only from localhost
    # listen localhost:80;

    server_name _;

    location /status {
        stub_status;
    }
}
```

Kunjungi subpath /status dari alamat ip mesin, jika konfigurasi benar, akan tertampil halaman yang menunjukkan jumlah koneksi yang dilayani oleh nginx
### 2. Install dan jalankan nginx-prometheus-exporter
Program ini berfungsi untuk melakukan mengubah data yang disajikan oleh modul stub_status nginx menjadi format yang sesuai untuk database prometheus. Data yang telah diformat secara default bisa diakses melalui port 9113

- **Unduh dan pasang binary secara manual**

	```bash
	$ wget https://github.com/nginx/nginx-prometheus-exporter/releases/download/v1.5.0/nginx-prometheus-exporter_1.5.0_linux_amd64.tar.gz
	$ tar xvf nginx-prometheus-exporter_1.5.0_linux_amd64.tar.gz
	$ sudo mv nginx-prometheus-exporter /usr/local/bin
	$ sudo chmod +x /usr/local/bin/nginx-prometheus-exporter
	```
	
- **Buat systemd unit file untuk menjalankan nginx-prometheus exporter setiap startup**

	```systemd
	# /etc/systemd/system/nginx-prometheus-exporter.service
	[Unit]
	Description=nginx-prometheus-exporter
	After=network.target
	[Service]
	Type=simple
	ExecStart=/usr/local/bin/nginx-prometheus-exporter -nginx.scrape-uri=http://localhost:80/status
	[Install]
	WantedBy=multi-user.target
	```

	Jalankan `sudo systemctl enable --now nginx-prometheus-exporter.service` untuk mengaktifkan service

### 3. Install dan konfigurasi Prometheus
- **Install prometheus melalui apt**
	Pada distro debian, prometheus tersedia pada repository apt dan bisa diinstall dengan menjalankan `sudo apt install prometheus`, kemudian jalankan `sudo systemctl enable --now prometheus.service` supaya program berjalan setiap startup
- **Konfigurasi prometheus**
	Edit konfigurasi prometheus untuk menambahkan job untuk melakukan scraping pada data yang telah disediakan oleh nginx-prometheus-exporter
		
	```yaml
	#/etc/prometheus/prometheus.yml
	scrape_configs:
	# The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
	- job_name: "nginx-prometheus-exporter"
	static_configs:
	  - targets: ["localhost:9113"]
	```
		
Prometheus sekarang telah berjalan dan bisa diakses melalui port 9090 secara default

### 4. Install dan jalankan Grafana
- **Install grafana dengan menambahkan repo grafana ke apt**

    ```bash
    $ wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null
    $ echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
	$ sudo apt install grafana
    ```
- **Pastikan grafana berjalan setiap startup melalui systemd**
    ```bash
    $ sudo systemctl enable --now grafana-server.service 
    ```
Grafana telah berjalan dan dapat diakses melalui port 3000. Untuk dapat login ke web interface, dapat digunakan username 'admin' dengan password 'admin' secara default.
### 5. Buat dashboard pada grafana untuk memvisualisasikan log nginx
Setelah grafana berjalan, web interface dapat digunakan untuk membuat dashboard dengan mode visualisasi yang relevan dengan data yang ingin dimonitor. Dashboard untuk memonitor koneksi nginx telah tersedia dan kita hanya perlu menspesifikasikan sumber data yang dipakai, yang dalam contoh ini adalah prometheus, dan url yang bisa digunakan untuk mengakses data tersebut.
