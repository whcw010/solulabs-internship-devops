# Text Manipulation

### Stream

Dalam sistem operasi Linux, stream adalah aliran data yang dapat dibaca atau ditulis oleh program.
Tipe Stream:

- **_stdin (standard input)_**: input data, biasanya dari keyboard atau file.
- **_stdout (standard output)_**: output utama, biasanya tampil di terminal.
- **_stderr (standard error)_**: pesan kesalahan, juga tampil di terminal.

### Stream Redirection (>, >>, |, <)

Operator redirection digunakan untuk mengarahkan aliran data antar program atau file.
Operator:

- `>` :Mengalihkan output ke file baru (menimpa file lama).
- `>>`: Menambahkan output ke bagian akhir file (tanpa menimpa isi sebelumnya).
- `<` : Mengambil input dari file, bukan dari keyboard.
- `|` : Mengarahkan output dari perintah di kiri menjadi input bagi perintah di kanan (piping).

Contoh:

```bash
$ echo "Hello world" > file.txt        # Tulis ke file baru
$ echo "Append line" >> file.txt       # Tambah ke akhir file
$ cat file.txt | grep "Hello"          # Filter baris dengan kata Hello
$ sort < unsorted.txt > sorted.txt     # Urutkan isi file dan simpan hasilnya
```

### grep

grep digunakan untuk mencari regular expression dalam file atau stream.

Contoh:

```bash
$ grep "error" /var/log/syslog          # Cari kata "error" di syslog
$ ps aux | grep "sshd"                  # Cari proses sshd yang sedang berjalan
$ grep -i "warning" log.txt             # Pencarian tidak case-sensitive
$ grep -r "main" src/                   # Cari secara rekursif di folder src
```

### tail

Program tail membaca bagian terakhir dari suatu file atau stream.

Contoh:

```bash
$ journalctl -u sshd.service | tail            # baris terakhir log sshd
$ journalctl -u sshd.service | tail -n 5       # 5 baris terakhir
$ journalctl -u sshd.service | tail -f         # Monitor log secara real-time, keluar dengan ^C
```

### head

Program head membaca bagian pertama dari suatu file atau stream.

Contoh:

```bash
$ cat filename.txt | head             # 10 baris pertama
$ cat filename.txt | head -n 3        # 3 baris pertama
```

### sed (Stream Editor)

Program sed digunakan untuk melakukan text editing, seperti mengganti, menghapus, atau menyisipkan teks dalam file/stream.

Syntax:

```bash
$ sed [OPTIONS] [SCRIPT] [FILENAME]
```

Script yang paling sering digunakan dari program sed adalah text replacement
Contoh:

```bash
$ sed 's/foo/bar/' file.txt             # Ganti kata pertama "foo" jadi "bar" di setiap baris
$ sed 's/foo/bar/g' file.txt            # Ganti semua "foo" jadi "bar" di setiap baris
$ sed -i 's/foo/bar/g' file.txt         # Edit file langsung
$ sed '/error/d' file.txt               # Hapus baris yang mengandung "error"
$ sed -n '1,5p' file.txt                # Cetak hanya baris 1–5
```

### awk

Program awk adalah pattern scanning and processing language yang digunakan untuk memproses dan menganalisis data berbasis teks (terutama data yang terstruktur dalam kolom, seperti log atau csv).

Syntax Dasar:

```bash
$ awk 'pattern {action}' [file]
```

Konsep Penting:

- $0 : seluruh baris teks
- $1, $2, ... : kolom ke-1, ke-2, dst.
- NR : nomor baris (Number of Record)
- NF : jumlah kolom pada baris saat ini (Number of Fields)

Contoh Penggunaan:

```bash
$ awk '{print $1}' file.txt                # Cetak kolom pertama
$ awk '{print $1, $3}' file.txt            # Cetak kolom ke-1 dan ke-3
$ awk '/error/ {print $0}' log.txt         # Cetak baris yang mengandung "error"
$ awk -F: '{print $1}' /etc/passwd         # Gunakan ':' sebagai pemisah kolom
$ awk '{sum += $2} END {print sum}' data   # Jumlahkan nilai di kolom ke-2
$ ps aux | awk '{print $1, $11}'           # Tampilkan user dan nama proses
```

### Kombinasi Command

Pada dapat dilakukan kombinasi beberapa program teks menggunakan pipe (|).

Contoh:

```bash
$ cat access.log | grep "404" | awk '{print $7}' | sort | uniq -c | sort -nr
```

Penjelasan:

- Baca file "access.log"
- Ambil baris yang mengandung "404"
- Cetak kolom ke 7 (URL)
- Urutkan dan hitung kemunculan tiap URL unik
- Urutkan hasil berdasarkan jumlah terbanyak
