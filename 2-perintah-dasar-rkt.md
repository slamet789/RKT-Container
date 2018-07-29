# Perintah Dasar rkt

**Mengunduh Gambar (ACI)**

rkt menjalankan aplikasi-aplikasi yang dikemas sesuai dengan spesifikasi Open-source App Container Image. ACI terdiri dari filesystem root dari kontainer aplikasi, manifes, dan tanda tangan opsional.

ACI diberi nama dengan struktur mirip URL. Skema penamaan ini memungkinkan untuk penemuan ACI yang terdesentralisasi, tanda tangan terkait, dan kunci publik. rkt menggunakan petunjuk ini untuk menjalankan penemuan meta.

* trust  
* fetch 

**Menjalankan Pods**

rkt dapat mengeksekusi ACI yang diidentifikasi oleh nama, hash, path file lokal, atau URL. Jika ACI belum di-cache pada disk, rkt akan berusaha mencari dan mengunduhnya. Untuk menggunakan layanan metadata rkt, aktifkan pendaftaran dengan tanda -mds-register ketika memohonnya.

* run
* stop
* enter
* prepare
* run-prepared

**Pemeriksaan dan manajemen pod**

rkt menyediakan perintah untuk daftar, mendapatkan status, dan membersihkan pod-nya.

* list
* status
* export
* gc
* rm
* cat-manifest

**Berinteraksi dengan penyimpanan gambar lokal**

rkt menyediakan perintah untuk mendaftar, memeriksa, dan mengekspor gambar di toko lokalnya.

* image

**Layanan Metadata**

Layanan metadata membantu aplikasi menjalankan introspeksi lingkungan eksekusi mereka dan menegaskan identitas pod mereka.

* metadata-service

**Layanan API**

Layanan API memungkinkan klien untuk mendaftar dan memeriksa pod dan gambar yang berjalan di bawah rkt.

* api-service

**Lain-lain**

* version
* config
