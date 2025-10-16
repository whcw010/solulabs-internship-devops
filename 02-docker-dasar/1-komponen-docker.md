# Komponen untuk Membuat Container Docker
Docker adalah platform yang memungkinkan deployment aplikasi di dalam container. Container adalah environment terisolasi yang berisi semua komponen yang dibutuhkan untuk menjalankan aplikasi (kode, library, dependensi, dll), tanpa perlu bergantung pada sistem host. Dengan Docker, aplikasi dapat berjalan secara konsisten di berbagai environment

### Docker Engine
Docker engine adalah inti dari sistem Docker. Komponen ini menangani semua operasi pengelolaan container dan image pada host. Komponen ini terdiri dari tiga bagian:
- ***Docker daemon***: proses/service yang yang mengelola semua komponen docker seperti container, image, network, volume, dll
- ***Docker CLI***: Program command line untuk melakukan manajemen docker dari terminal
- ***Docker API***: Interface yang menspesifikasikan bagaimana 3rd party application dapat berinteraksi dengan daemon untuk melakukan manajemen docker

### Docker Image 

Docker image adalah template read-only yang berisi sistem file dan semua dependensi untuk menjalankan aplikasi. Image bisa didapat dari docker registry atau dibuat dengan Dockerfile. Dockerfile adalah file yaml yang berisi instruksi step-by-step untuk membuat image.

Contoh Dockerfile:
```Dockerfile
# menentukan base image. image yang dijadikan base image bisa berasal pada registry lokal atau dockerhub
FROM node
# menyalin aplikasi ke dalam image pada direktori /app
COPY . /app
# menentukan working direktori dari image
WORKDIR /app
# menginstal dependensi node pada image
RUN npm install
# menentukan command yang berjalan ketika image dijalankan
CMD ["npm", "run", "dev"]
```
Image kemudian dapat di-build dengan menjalankan command `docker build -t __image-name__ .` pada direktori Dockerfile.

### Docker Container
Container adalah instance dari suatu image yang berjalan menjalankan proses yang ditentukan pada Dockerfile. Ketika container dijalankan, Docker membuat lapisan writable di atas image sehingga aplikasi bisa beroperasi tanpa mengubah image asli. Container docker berjalan pada environment yang terisolasi dari host machine dan container lainnya tetapi dapat melakukan komunikasi melalui docker network.

