# Manipulasi File Permission dan Ownership

### Manipulasi File Permission
Modifikasi permission atau mode dari file bisa dilakukan dengan command `chmod` yang memiliki syntax berikut:
```bash
$ chmod [OPTION] [MODAL] [FILENAME]
```
Argumen modal menentukan bagaimana mode dari file akan diubah. Argumen ini dapat menggunakan notasi simbolik dan octal.

#### Symbolic Modal
```
[u|g|o|a][-=+][permission]
```
Subject(u/g/o/a): entitas yang permission-nya dimodifikasi
- **u**: user yang memiliki file
- **g**: group yang memiliki file
- **o**: user yang bukan pemilik atau anggota group owner
- **a**: semua user (memodifikasi permission untuk user, group, dan other)

Operator(- / = / +): bagaimana permission dimodifikasi 
- **-**: permission yang dispesifikasikan dihapus untuk entitas yang ditentukan
- **=**: entitas hanya akan memiliki permission yang ditentukan
- **+**: permission yang dispesifikasikan ditambah untuk entitas yang ditentukan

Permission(r,w,x,s): semua permission yang akan dimodifikasi 

Contoh:
- `o+rw`: menambah permission read dan write untuk untuk user yang termasuk dalam kelompok 'other'
- `u=rx`: memodifikasi sehingga user pemilik file hanya memiliki permission read dan execute pada file
- `o-x`: menghapus permission execute file untuk user yang termasuk dalam kelompok 'other'

#### Octal Modal
Mode dari file akan dimodifikasi menjadi mode yang dispesifikasikan dengan notasi oktal yang telah dibahas pada bagian sebelumnya

Contoh:
```bash
$ chmod 0644 filename.txt
```
Command diatas akan mengubah mode file menjadi `-rw-r--r--`
#### Option
Berikut adalah option yang sering digunakan dengan program chmod:
- ***-R***: Menerapkan pergantian mode pada semua subdirektori dan file yang ada dibawah direktori yang mode-nya dimodifikasi.
- ***-c***: melaporkan semua perubahan mode yang terjadi pada standard output.
### Manipulasi File Ownership
Kepemilikan file pada sistem Linux dapat diubah dengan program `chown` yang memiliki syntax sebagai berikut:
```bash
$ chown [OPTION] [OWNER:GROUP] [FILE]
```
Berikut adalah contoh penggunaan umum dari program `chown`:
- Mengganti user dan group yang memiliki suatu file:
```bash
$ chown user:group [FILENAME]
```
- Hanya mengganti user yang memiliki suatu file:
```bash
$ chown user [FILENAME]
```
- Hanya mengganti group yang memiliki suatu file:
```bash
$ chown :group [FILENAME]
```
- Menerapkan pergantian kepemilikan secara rekursif kepada semua subdirektori dan file yang ada dibawah suatu direktori
```bash
$ chown -R [OWNER:GROUP] [DIRECTORY]
```
