# Fetch Image rkt dan Run Docker Images dengan rkt

## Fetch Image rkt

Ada beberapa cara untuk fetch image pada rkt, yaitu :

**1.Fetch dengan Meta Discovery**

Kita dapat langsung meng-*fetch* ACI melalui *meta discovery*-nya.
Syntax yang kira gunakan adalah

``rkt<spasi>fetch<spasi>alamat_meta_disovery``

Namun terkadang terjadi error saat kita menjalankan perintah ini kaerna masalah *permission*.

Contoh kita jalankan syntax:

``rkt fetch coreos.com/etcd:v2.0.0``

Maka akan muncul error :

![1fetchmetaerror](https://github.com/slamet789/RKT-Container/blob/3-fetch-n-run/pict/fetch/1fetchmetaerror.jpg)

Sehingga kita butuh perintah *sudo* agar tidak ada masalah *permission*, sehingga menjadi :

``sudo rkt fetch coreos.com/etcd:v2.0.0``

Maka hasilnya adalah :

![1.1fetchmeta](https://github.com/slamet789/RKT-Container/blob/3-fetch-n-run/pict/fetch/1.1fetchmeta.jpg)

Dari gambar di atas bisa kita lihat bahwa ACI selesai di *fetch* dan mempunya id **sha512-fa1cb92dc276b0f9bedf87981e61ecde**

Setelah selesai fetch, kita bisa mengecek nya dengan perintah :

``rkt image list``

maka akan muncul image yang teleh ter-*fetch*, yaitu :

![1.2fetchmetaimage](https://github.com/slamet789/RKT-Container/blob/3-fetch-n-run/pict/fetch/1.2fetchmetaimage.jpg)

**2.Fetch dari lokasi tertentu, contoh GitHub**

Hampir sama, dengan *fetch* pertama, yang berbeda adalah meta discovery diubah menjadi lokasi *image*, syntax yang kita gunakan adalah  :

``rkt<spasi>fetch<spasi>lokasi_image``

Contoh kita akan men-*fetch* image dari GitHub, maka kita gunakan perintah :

``sudo rkt fetch https://github.com/coreos/etcd/releases/download/v2.0.0/etcd-v2.0.0-linux-amd64.aci``

Maka hasilnya adalah :

![2.fetchgithub](https://github.com/slamet789/RKT-Container/blob/3-fetch-n-run/pict/fetch/2.fetchgithub.jpg)

ID image di sini sama dengan image pertama (dengan meta discovery) karena versinya sama yaitu v.2.0.0

**3.Fetch dari Docker Registry**

Kita dapat mendownload image pada Docker Registry untuk rkt, rkt secara otomatis akan meng-*convert* image docker menjadi ACI

Syntax yang kita gunakan adalah :

``rkt --insecure-options=image fetch docker://nama_image ``

Contoh kita men-*fetch* image busybox , maka kita gunakan perintah :

``rkt --insecure-options=image fetch docker://busybox``

Maka hasilnya adalah :

![3fetchdocker](https://github.com/slamet789/RKT-Container/blob/3-fetch-n-run/pict/fetch/3fetchdocker.jpg)


Image rkt-nya adalah **sda5120-2641121f72e23150b2e96efd301b1239**

Selanjutnya bisa kita cek dengan perintah :

``rkt image list``

yang hasilnya adalah :

![3image3](https://github.com/slamet789/RKT-Container/blob/3-fetch-n-run/pict/fetch/3image3.jpg)

Dari gambar diatas dapat kita lihat bahwa ada 2 image rkt yaitu yang pertama kita *fetch* dari meta discovery dan yang kita *fetch* dari Docker Registry.

## Run Docker Image dengan rkt

Untuk mengfetch image docker, pada awal nama image kita tambahi ``docker://`` dan kita juga menggunakan perintah ``--insecure-options=image`` karena image docker tidak support *signature verification* .

Contoh kita ingi me-*run* redis dengan perintah :

``rkt --insecure-options=image run docker://redis``

Maka tampilannya adalah :

![1runningreddis](https://github.com/slamet789/RKT-Container/blob/3-fetch-n-run/pict/run/1runningreddis.jpg)

Secara default jika kita tidak menambahkan apa-apa di belakang perintah ``docker://`` , maka akan secara default mengambil dari Docker Hub.

Contoh jika kita tidak mengambil dari Docker Hub :

``rkt --insecure-options=image fetch docker://quay.io/zanui/nginx``

Perintah di atas digunakan untuk *fetch* image docker dari quay.io, sehingga rkt akan mendownload dulu selanjutnya baru bisa kita jalankan, maka hasilnya adalah :

![2runnginx](https://github.com/slamet789/RKT-Container/blob/3-fetch-n-run/pict/run/2runnginx.jpg)

Seteleh terdownload , maka memiliki ID **sha512-c293740edd837c683f2e12dfe414a5a4**.

Selanjutnya kita jalankan image ini dengan perintah :

``rkt --insecure-options=image run sha512-c293740edd837c683f2e12dfe414a5a4``

jika masih ada masalah priviledge, sepreti gambar dibawah ini :

![2.1runerror](https://github.com/slamet789/RKT-Container/blob/3-fetch-n-run/pict/run/2.1runerror.jpg)

Maka tambahkan perintah ``sudo``, menjadi :

``sudo rkt --insecure-options=image run sha512-c293740edd837c683f2e12dfe414a5a4``

Maka hasilnya adalah :

![2.1runnginxok](https://github.com/slamet789/RKT-Container/blob/3-fetch-n-run/pict/run/2.1runnginxok.jpg)



