# Menjalankan Jenkins Secara Native pada Distro Debian

### 1. Menambahkan repository jenkins ke daftar repo apt

Supaya Jenkins bisa diinstall dan terupdate secara central melalui apt, perlu ditambahkan repository dari jenkins beserta signing key yang bersangkutan

```bash
$ sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
$ echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```
Setelah itu, jenkins dapat diinstall melalui apt 
```bash
$ sudo apt update
$ sudo apt install jenkins
```

### 2. Menjalankan service Jenkins setiap startup

```bash
$ sudo systemctl enable --now jenkins.service
```
Dengan metode ini, Jenkins akan berjalan sebagai user 'jenkins'. Lokasi direktori jenkins_home dapat dilihat melalui 'sudo systemctl cat jenkins.service', file hanya bisa diedit oleh user dengan permission yang sesuai. Untuk menjalankan jenkins sebagai user tertentu, edit systemd unit file dan menambahkan 
```systemd
[Service]
User=someuser
Group=someuser
```
### 3. Login ke web interface Jenkins
Secara default, web-ui Jenkins dapat diakses melalui port 8080, saat pertama kali mengaakses Jenkins, akan diminta menginput admin password yang lokasinya dispesifikasikan pada halaman login. Setelah itu perlu dibuat akun admin dengan menginput username dan password baru.
