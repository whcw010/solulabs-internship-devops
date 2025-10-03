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

### Contoh kasus: Mendeploy kode ke dockerhub menggunakan instance Jenkins yang berjalan pada container

Pada contoh ini, akan dilakukan deployment kode yang di-host di github dengan membuat image docker berdasarkan Dockerfile yang berada pada repo tersebut. 

#### Container image
Karena image docker jenkins secara default tidak terdapat docker, perlu dibuat image docker custom menggunakan Dockerfile. Pada Dockerfile dispesifikasikan untuk menginstall package docker.io pada image Jenkins dan memberikan user jenkins permission untuk melakukan operasi docker dengan menambahkan user jenkins ke grup docker.

```Dockerfile
FROM jenkins/jenkins
USER root
RUN apt update && apt install docker.io -y
RUN usermod -aG docker jenkins
USER jenkins
```
#### Docker compose

Dengan metode ini, dilakukan volume binding pada docker socket yang dimiliki oleh host machine supaya jenkins dapat menggunakan instance jenkins dari host tanpa perlu menjalankan instance docker daemon ganda pada container jenkins.  

```yaml
services:
  jenkins:
    container_name: jenkins
    build: ./
    image: jenkins
    restart: unless-stopped
    ports:
      - "8080:8080"
    volumes:
      - ./jenkins_data/jenkins_home:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock 
```
#### Buat pipeline jenkins yang di-trigger oleh webhook
Berikut adalah spesifikasi pipeline yang diguanakan untuk mendeploy kode pada dockerhub. Pipeline ini terdiri dari empat stages, yang meliputi fetching repositori dari github, melakukan building image berdasarkan dockerfile pada repositori, kemudian mengupload repositori ke dockerhub, setelah deployment juga dilakukan penghapusan image local hasil build untuk menghemat space. 
```jenkins
pipeline {
  environment {
    registry = "whcw010/jenkins-test"
    registryCredential = 'dockerhub'
    dockerImage = ''
}
agent any
stages {
  stage('Clone') {
    steps {git 'https://github.com/whcw010/docker-node-hello-world'}
    }
  stage('Build'){
    steps{script {dockerImage = docker.build registry + ":$BUILD_NUMBER"}}
  }
  stage('Deploy') {
    steps{
      script {docker.withRegistry( '', registryCredential) {dockerImage.push()}}
      }
  }
  stage('Cleanup') {
    steps{sh "docker rmi $registry:$BUILD_NUMBER"}
  }
}
}
```
Supaya pipeline ini dapat di-trigger menggunakan webhook, pilih opsi 'Triggers build remotely' pada konfigurasi trigger pipeline. Spesifikasikan authentication token dari webhook yang akan digunakan pada url webhook. Untuk keperluan autentikasi juga diperlukan api key user yang bisa digenerate melalui halaman `JENKINS_URL/user/{username}/security`. 

setelah langkah-langkah diatas selesai, pipeline dapat di trigger dengan request HTTP berikut:
```bash
$ curl -X POST -L --user USERNAME:USER_API_TOKEN 'JENKINS_URL/job/JOB_NAME/build?token=TOKEN_NAME'
```

#### Pastikan URL webhook dapat dipanggil melalui internet
Untuk memastikan bahwa pipeline dapat di-trigger oleh Github actions, URL diatas harus dapat diakses melalui jaringan publik. Ini bisa dilakukan dengan meng-host Jenkins pada machine yang memiliki public IP atau dengan menggunakan layanan proxy seperti ngrok atau zrok.

Untuk skenario ini, digunakan layanan zrok untuk mengekspos port local dari host host machine:

```bash
$ zrok share public localhost:8080
```
Dengan menjalankan perintah diatas, akan didapatkan url random yang dapat diakses publik tanpa VPN. URL ini akan digunakan oleh Github actions untuk memanggil webhooks jenkins 

#### Buat Github actions untuk memanggil webhook setiap ada commit baru 

Berikut adalah github actions untuk memanggil webhook setiap terjadi push di branch master:
```yaml
name: trigger jenkins webhook on Push to Master
on:
  push:
    branches:
      - master
jobs:
  trigger:
    runs-on: ubuntu-latest
    steps: 
    - name: call jenkins webhook
      shell: bash
      env:
        URL: ${{ vars.JENKINS_WEBHOOK_URL }}
        USERNAME: ${{ secrets.JENKINS_USERNAME }}
        PASSWORD: ${{ secrets.JENKINS_API_KEY }}
        TOKEN: ${{ secrets.JENKINS_WEBHOOK_TOKEN }}
        JOB_NAME: ${{ vars.JENKINS_JOB_NAME }}
      run: curl -X POST -L --user $USERNAME:$PASSWORD $URL/job/$JOB_NAME/build?token=$TOKEN
```
Informasi sensitif yang diperlukan untuk memanggil webhook disimpan dengan fitur github secrets. Pipeline di-trigger dengan menjalankan program curl untuk memanggil webhook url dengan kredensial yang telah ditentukan pada langkah sebelumnya
