# Uptime Kuma

Uptime Kuma adalah self-hosted monitoring service yang berfungsi untuk memantau uptime dari server, website, database, maupun service lain.  
Kelebihan utama Uptime Kuma adalah setup yang mudah, di mana hampir seluruh konfigurasi seperti menambah dan monitor mengatur notifikasi dapat dilakukan tanpa menyentuh file konfigurasi atau command line.

## Deployment (Docker Compose)

Gunakan konfigurasi berikut untuk menjalankan Uptime Kuma menggunakan Docker Compose:

```yaml
services:
  uptime-kuma:
    image: louislam/uptime-kuma:2
    container_name: uptime-kuma
    restart: always
    ports:
      - "3001:3001"
    volumes:
      - ./data:/app/data
```

### Cara menjalankan

```bash
docker compose up -d
```

### Akses

-   URL: **http://localhost:3001**
-   Setup akun admin untuk login.
-   Semua konfigurasi dilakukan melalui UI 
    

## Monitor

Sistem monitoring di Uptime Kuma berfungsi untuk mendeteksi status UP/DOWN dari suatu layanan berdasarkan interval tertentu. Setiap monitor memiliki:

- Interval (seberapa sering pengecekan dilakukan)
- Retry (jumlah percobaan ulang sebelum dianggap DOWN atau UP)
- Timeout
- Status log 
- Maintenance mode (pengecualian downtime)

Berikut penjelasan lebih lengkap untuk setiap tipe monitor:

### HTTP / HTTPS Monitor

Memantau website atau API endpoint dengan fitur:

-   Cek kode status HTTP 
-   Cek response time
-   Validasi keyword pada response body
-   Cek expiry SSL 
-   Opsi HTTP method, header, dan data (POST/PUT)

### Ping Monitor

Mengirim ping kepada host untuk memastikan host dapat dijangkau.

Parameter utama:
-   Interval
-   Jumlah retry
-   Timeout


### Port Monitor

Mengecek apakah port tertentu terbuka dan dapat menerima koneksi.

### DNS Monitor

Melakukan DNS lookup dan memverifikasi apakah hasilnya sesuai ekspektasi. Seperti untuk memastikan suatu domain resolve ke IP tertentu

### Push Monitor (Heartbeat)

Service eksternal mengirim request ke Uptime Kuma melalui URL unik yang digenerate Uptime Kuma. Uptime Kuma akan menganggap monitor down jika tidak menerima ping dalam interval yang ditentukan.

### Docker Monitor 

Memeriksa status container, namun biasanya membutuhkan integrasi custom.


## Notification

Notifikasi pada Uptime Kuma adalah sistem pemberitahuan otomatis yang menginformasikan perubahan status layanan yang dimonitor, seperti ketika sebuah layanan mengalami downtime atau kembali online, akan dikirimkan notifikasi melalui service yang telah dikonfigurasi. Setup notifikasi dilakukan melalui webui dengan memasukkan data yang dibutuhkan pada field yang ditentukan. 

Berikut adalah beberapa service yang didukung untuk pengiriman notifikasi:
- Telegram
- Email
- Webhook call
- Discord
