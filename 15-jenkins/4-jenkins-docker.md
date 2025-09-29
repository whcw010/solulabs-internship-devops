# Menjalankan Jenkins Menggunakan Docker

### Base jenkins image

```yaml
# compose.yaml
services:
  jenkins:
    container_name: jenkins
    image: jenkins/jenkins
    restart: unless-stopped
    ports:
      - "8080:8080"
    volumes:
      - "./jenkins_data/jenkins_home:/var/jenkins_home"
```
Diatas adalah spesifikasi docker compose untuk menjalankan Jenkins pada port 8080 pada host machine. Direktori jenkins_home pada container dapat diakses melalui direktori ./jenkins/data/jenkins_home relatif dari lokasi file compose.yaml pada host machine.

Jenkins dapat dijalankan dengan perintah:

```bash
$ docker compose up -d
```
### Custom jenkins image

Secara default, image jenkins/jenkins tidak terdapat tools yang mungkin dibutuhkan pada proses deployment tertentu, seperti make atau docker. Dalam skenario ini, diperlukan untuk dibuatnya image custom menggunakan Dockerfile untuk menambahkan tools yang diperlukan
Contoh:
```Dockerfile
FROM jenkins/jenkins
USER root
RUN apt-get update && apt-get install -y build-essential
USER jenkins
```
File docker compose:
```yaml
services:
  jenkins:
    container_name: jenkins
    image: jenkins-custom
    build: /jenkins/Dockerfile/location
    restart: unless-stopped
    ports:
      - "8080:8080"
    volumes:
      - "./jenkins_data/jenkins_home:/var/jenkins_home"
```
