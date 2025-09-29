### Konsep Dasar
Jenkins adalah sistem Continuous Integration (CI) yang dapat mengotomasi berbagai langkah pada pengembangan software seperti building, testing, deployment, dan documentation. Jenkins bekerja dengan memonitor perubahan pada repositori kode seperti github atau codeberg kemudian menjalankan workflow yang mengandung langkah-langkah untuk mendeploy kode.

Workflow pada jenkins disebut **pipeline** yang merupakan file yang menspesifikasikan langkah-langkah yang akan dijalankan dalam proses deployment program. Setiap pipeline pada jenkins dibagi menjadi beberapa **stages**. Pembagian menjadi **stages** memungkinkan developer untuk memishakan proses deployment menjadi fase-fase tertentu seperti checkout, testing, building, dll, untuk mempermudah proses diagnosa ketika terjadi kegagalan pada **stage** tertentu.

**Pipeline** bisa dimulai secara manual atau secara otomatis melalui **trigger**. **Trigger** otomatis dapat berupa melakukan monitoring pada repository untuk commit baru, atau bisa melalui webhook yang bisa diaktifkan oleh layanan eksternal seperti github action.

Jenkins menggunakan sistem **node** untuk mendistribusikan beban kerja pada beberapa sistem. Setiap **node** merupakan instance jenkins yang berjalan pada suatu mesin(fisik atau virual) terhubung yang dapat diassign untuk menjalankan pipeline. 
