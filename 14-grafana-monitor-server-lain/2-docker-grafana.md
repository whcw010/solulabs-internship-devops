# Visualisasi Log Nginx Menggunakan Grafana yang Berjalan Secara Native

Selain menjalankan secara native, solusi alternatif adalah dengan menggunakan docker untuk menjalankan grafana beserta software pendukungnya. Dengan metode ini, dapat dipastikan bahwa sistem akan berjalan dengan behaviour yang sama pada host machine yang berbeda. Proses deployment juga tersimplifikasi dan grafana dapat di-deploy dengan satu perintah di mesin manapun.

Disini digunakan docker compose untuk 

Pada contoh ini, diasumsikan bahwa docker telah terpasang pada mesin host dan nginx telah berjalan dan terkonfigurasi untuk mengekspos halaman stub_status pada url http://localhost:80/status

### 0. Working Directory

```
$ tree .
.
├── compose.yaml
├── npe
│   ├── Dockerfile
│   └── nginx-prometheus-exporter <- binary executable dari program nginx-prometheus-exporter
└── prometheus
    └── prometheus.yml <- konfigurasi prometheus, sudah diatur untuk melakukan scrapping pada halaman status nginx
```

Diatas merupakan detail dari directory dan file-file yang diperlukan untuk menjalankan grafana melalui docker-compose 

```yaml
    #./compose.yaml
    services:
      prometheus:
        ...
      nginx-prometheus-exporter:
        ... 
      grafana:
        ...
```

File compose.yaml terdiri dari service prometheus, nginx-prometheus-exporter, dan grafana yang detailnya adalah sebagai berikut:

### 1. services.prometheus
```yaml
...
prometheus:
    image: prom/prometheus
    container_name: prometheus
    restart: unless-stopped 
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - 9090:9090
...
```

Service ini menggunakan fitur volumes dari docker sehingga konfigurasi prometheus.yml dapat di-edit dari luar container dan file konfigurasi tetap tersimpan ketika image container dihapus karena update atau rekonfigurasi.

### 2. services.nginx-prometheus-exporter

```yaml
...
  nginx-prometheus-exporter:
    image: nginx-prometheus-exporter
    build: ./npe
    container_name: nginx-prometheus-exporter
    restart: unless-stopped
    environment:
      NGINX_STATUS_URL: "http://172.17.0.1/status"
    ports:
      - 9113:9113
...
```
Service ini menggunakan image yang di-build berdasarkan spesifikasi Dockerfile yang terdapat pada direktori ./npe.
```Dockerfile
# ./npe/Dockerfile
FROM alpine
COPY ./nginx-prometheus-exporter .
ENV NGINX_STATUS_URL "http://localhost/status"
EXPOSE 9113
CMD ./nginx-prometheus-exporter --nginx.scrape-uri=$NGINX_STATUS_URL
```
Ketika container dijalankan, program nginx-prometheus-exporter akan berjalan pada port 9113. Url scrapping dapat diatur melalui environment variable. Pada file compe.yaml, environment variable tersebut diatur menjadi "http://172.17.0.1/status" yang merupakan alamat host machine pada network docker.

### 3. services.grafana
```yaml
...
  grafana:
    image: grafana/grafana
    container_name: grafana
    restart: unless-stopped
    ports:
      - 3000:3000
...
```
Konfigurasi ini akan memulai service grafana yang bisa diakses melalui port 3000. Pembuatan dashboard untuk visualisasi data dapat dilakukan melalui web interface. Fitur *volumes* dari docker dapat digunakan di service ini supaya dashboard yang telah dibuat melalui web interface dapat presisten jika image container dihapus.
