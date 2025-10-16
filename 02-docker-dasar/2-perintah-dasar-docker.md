# Perintah dasar docker

Berikut adalah command-command docker yang sering digunakan untuk pengelolaan container dan image docker:

### Image management
- `docker images`:
    Tampilkan semua image docker yang telah tersimpan pada host machine 
- `docker pull __image name__`:
    Download image docker dari registry untuk disimpan pada host machine
- `docker image rm __img0__ ` / `docker rmi __img0__`:
    Hapus image dari penyimpanan lokal

### Container management
- `docker ps -a`:
    Tampilkan semua container baik yang berjalan atau berhenti
- `docker run --name __container name__ __img0__`:
    Menjalankan image __img0__ dengan menspesifikasikan nama container 
- `docker exec -it __container name__ sh`:
    Menjalankan program shell interaktif pada container atau masuk ke environment container  
- `docker logs __container name__`:
    membaca log yang di-generate oleh container 
- `docker start __container name__`
    Menjalankan kembali container yang sudah pernah dibuat
- `docker stop __container name__`
    Menghentikan container yang sedang berjalan
- `docker rm __container name__`
    Menghapus container yang sudah berhenti
