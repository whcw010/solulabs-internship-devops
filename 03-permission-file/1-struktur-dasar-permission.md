# Struktur Dasar Permission dan Ownership
Setiap file pada sistem operasi Linux memiliki file mode yang bisa dilihat dengan command `ls -l`

Contoh:
```bash
$ ls -l
total 8
-rw-r--r-- 1 usr grp 156 Oct 17 10:51 1-struktur-dasar-permission.md
-rw-r--r-- 1 usr usr 157 Oct 10 16:22 2-manipulasi-permission-file.md
drwxr-xr-x 1 usr grp 0 Oct 17 10:53 directory
```
### Ownership
Dalam konteks file permission pada sistem operasi Linux, kepemilikan user tertentu dari suatu file dapat dibagi menjadi tiga:
- ***User***: Pemilik (owner) dari file, ditunjukkan pada kolom ketiga dari output command `ls -l`.
- ***Group***: Group yang memiliki file, ditunjukkan pada kolom keempat. Nama group yang sama dengan nama user pemilik file mengindikasikan bahwa file hanya dimiliki oleh satu user.
- ***Other***: User yang tidak termasuk dari user dan group owner dari file.

### Permission
Kolom pertama  dari output command `ls -l` menunjukkan file mode dari masing-masing file. Kolom ini terdiri dari 10 karakter yang bersama dengan ownership menentukan siapa yang memiliki file tersebut beserta operasi yang bisa dilakukan oleh masing-masing user pada komputer tersebut.

Berikut adalah penjabaran dari masing-masing karakter pada kolom filemode diatas:
- ***Karakter pertama: File type***:  
    Menunjukkan tipe dari file yang bersangkutan. Common values:
    - ***d***: directory
    - ***l***: symbolic link
    - ***-***: regular file

- ***File permission***:
    Sembilan karakter terakhir dari kolom file mode terdiri dari tiga bagian yang masing-masing menentukan read, write, dan execute permission untuk user yang termasuk owner, group owner, atau other secara berurutan dalam kepemilikan dari file. 

    Setiap bagian ini terdiri dari tiga karakter yang mengindikasikan apakah user terkait memiliki permission untuk melakukan operasi tertentu dengan file:
    - ***read permission***: 'r' jika memiliki izin, '-' sebaliknya
    - ***write permission***: 'w' jika memiliki izin, '-' sebaliknya
    - ***execute permission***: 'x' jika memiliki izin, '-' sebaliknya

Contoh:
```
-rw-r--r--
```
Breakdown:
- karakter pertama `-` menunjukkan bahwa file yang tersangkut adalah file biasa.
- karakter 2,3,4 `rw-` menunjukkan bahwa owner punya izin untuk melakukan operasi read dan write pada file, tetapi tidak memiliki izin untuk mengeksekusi file.
- karakter 5,6,7 `r--` menunjukkan bahwa group yang memiliki file hanya dapat membaca file.
- karakter 8,9,10 `r--` menunjukkan bahwa user selain owner atau anggota grup pemilik file hanya memiliki izin untuk membaca file.

### Special File Permissions (setuid, setgid, sticky bit)
Selain dari tiga permission diatas, Linux juga menyediakan permission spesial yang memungkinkan suatu file executable untuk berjalan sebagai user pemilik file, meskipun bukan owner yang mengeksekusi file tersebut. Ini dilakukan dengan mengubah UID atau GID dari proses yang di-spawn oleh executable sehingga dapat berjalan dengan wewenang yang sama dengan user pemilik file.

Contoh:
```bash
$ ls -l /usr/bin/passwd
-rwsr-xr-x 1 root root 80856 Jun 27 14:35 /usr/bin/passwd
```
Pada blok owner permission dari kolom filemode, karakter ketiga yang biasanya bernilai `x` atau `-` diganti dengan `s` yang menunjukkan bahwa permission setuid aktif pada file. Dengan permission setuid ini, proses yang di-spawn oleh `/usr/bin/passwd` dapat beroperasi sebagai owner file (root) seperti melakukan operasi write pada file `/etc/shadow` meskipun file dieksekusi user yang ada dalam kelompok 'other'.

Permission setgid memiliki fungi serupa, yaitu mengubah GID dari proses yang di-spawn oleh executable terkait menjadi GID dari group pemilik file. Permission ini mengganti permission group execute dengan karakter `s`.

Permission sticky bit mengganti other execute permission pada file mode. File pada directory yang memiliki sticky bit hanya bisa dihapus oleh pemilik file dan user root.
### Notasi Oktet
Permission dari sebuah file dapat direpresentaskikan dengan notasi oktal yang terdiri dari 4 digit. Setiap digit merupakan hasil penjumlahan dari angka-angka yang terasiosasi dengan permission tertentu.

Berikut adalah breakdown untuk semua digit pada notasi oktal:
- ***Digit Pertama***:

    Menentukan special permission yang di-set untuk file:
    - 1: sticky bit
    - 2: setgid
    - 4: setuid

- ***Digit 2-4***:

    Menentukan standard permission yang di-set untuk owner, group, dan other secara berurutan:
    - 1: execute
    - 2: write
    - 4: read

Untuk setiap digit dilakukan penjumlahan terhadap semua digit dengan permission yang aktif.

Contoh:
Notasi Oktet untuk file dengan permission `-rw-r--r--`:
- ***Digit pertama***: tidak ada special permission yang di-set -> 0
- ***Digit kedua***: permission read dan write di-set -> 0 + 2 + 4 = 6
- ***Digit ketiga***: hanya permission read yang di-set -> 0 + 0 + 4 = 4
- ***Digit keempat***: hanya permission read yang di-set -> 0 + 0 + 4 = 4

Notasi oktet: ***0644***
