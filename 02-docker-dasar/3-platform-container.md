# Platform untuk Container
Berikut adalah contoh platform alternatif dari docker yang memungkinkan deployment aplikasi dengan konsep containerization.

### Podman
Podman adalah platform containerization yang tidak menggunakan daemon untuk manajemen container. Pendekatan ini lebih aman karena manajemen container tidak bergantung pada proses yang berjalan sebagai root yang rentan dari privelege escalation. 

Podman menggunakan systemd sebagai perantara user dan sistem untuk menjalankan proses container. User dapat melakukan manajemen container langsung melalui systemd atau melalui interface yang disediakan oleh Podman yang berupa CLI dan API.

### Vagrant
Vagrant adalah tool untuk membuat dan memanajemen virtual machine. Vagrant memungkinkan untuk menjalankan VM secara replicable dengan menggunakan file konfigurasi Vagrantfile yang menspesifikasikan base image, alokasi resource, dan environment dari VM. Vagrant juga menyediakan interface yang memungkinkan user untuk melakukan manajemen VM seperti menjalankan, menghentikan, dan menghubungi instance virtual machine.

Karena menggunakan VM untuk virtualization, Vagrant memiliki lebih terisolasi daripada solusi lain yang menggunakan konsep container karena setiap instance memiliki root tersendiri. Akan tetapi, solusi virtualization yang menggunakan Vagrant memakan resource yang lebih banyak daripada solusi berbasis container.

