# RKT-Container
RKT Container
# Membuat dan Menjalankan Container dengan RKT
## Untuk membangun sebuah container di RKT, kita menggunakan appc acbuild tool. Kita mulai dengan aplikasi C yang sangat dasar :
```$ cat hello.c
#include <stdio.h>
int main (int argc, char** argv) {
  printf("Hello, world!\n");
  return 0;
}
$ gcc -o hello -static hello.c```
## Membuat image dengan acbuild mirip dengan membuat gambar Docker tetapi tanpa Dockerfile - yaitu, kita menjalankan urutan perintah acbuild. Kita dapat memasukkan ini ke dalam skrip untuk memberi kita satu baris untuk membangun image kita. Kita dapat menggunakan skrip sederhana ini untuk membuat image:
```$ cat appc-hello.sh 
#!/usr/bin/env bash
acbuild begin
acbuild set-name hello
acbuild copy hello /app/hello
acbuild set-working-directory /app
acbuild set-exec -- /app/hello
acbuild write --overwrite hello-latest-linux-amd64.aci```
## Jika kita menjalankan skrip ini, itu akan membangun image kita:
```$ ./appc-hello.sh 
Beginning build with an empty ACI
Setting name of ACI to hello
Copying host:hello to aci:/app/hello
Setting working directory to /app
Setting exec command [/app/hello]
Writing ACI to hello-latest-linux-amd64.aci```
## Sebagai hasilnya, kami memiliki file, halo-latest-linux-amd64.aci, di direktori kami saat ini. Ini adalah image kontainer appcont yang tidak terenkripsi dan tidak terenkripsi yang dapat kita jalankan dengan rkt. Kita bisa melihatnya di daftar image rkt dengan perintah:
```$ sudo rkt image list
ID          NAME                    IMPORT TIME LAST USED   SIZE    LATEST
sha512-c500b17b60fa hello                   3 minutes ago   3 minutes ago   1.6MiB  false```
## Kami dapat meluncurkan image ini sekarang :
```$ sudo rkt --insecure-options=image run hello-latest-linux-amd64.aci
image: using image from local store for image name coreos.com/rkt/stage1-coreos:1.0.0
image: using image from file hello-latest-linux-amd64.aci
networking: loading networks from /etc/rkt/net.d
networking: loading network default with type ptp
[296002.703623] hello[4]: Hello, world!```
## Seperti di Docker, container masih ada setelah eksekusi selesai cek dengan perintah:
```$ sudo rkt list
UUID        APP IMAGE NAME  STATE   CREATED     STARTED     NETWORKS
6e651372    hello   hello       exited  3 seconds ago   2 seconds ago```
## rkt menyertakan perintah praktis untuk membantu Anda membersihkan image dan container yang tidak terpakai. Untuk membersihkan image :
```$ sudo rkt gc```
## Untuk membersihkan pod (container) :
```$ sudo rkt image gc```
## Sekarang kami akan menjalankan sesuatu yang melakukan beberapa jaringan:
```$ git clone https://github.com/lukebond/demo-api
$ cd demo-api
$ sudo ./appc.sh
$ sudo rkt --insecure-options=image run demo-api-latest-linux-amd64.aci```
## Luncurkan terminal lain dan jalankan yang berikut untuk mengetahui alamat IP dan uji dengan curl:
```$ sudo rkt list
UUID        APP     IMAGE NAME      STATE   CREATED     STARTED     NETWORKS
55cb3a96    demo-api    lukebond/demo-api   running 4 minutes ago   4 minutes ago   default:ip4=172.16.28.7
6e651372    hello       hello           exited  2 days ago  2 days ago  
$ curl 172.16.28.7:9000
"Hello, world 172.16.28.7!"```
## Untuk mengakses log untuk container kami, kami menggunakan jurnal jurnal sistem, seperti:
```$ machinectl list
MACHINE                                  CLASS     SERVICE
rkt-e16bafd0-3b0b-4ade-b482-d6de42d35e8c container nspawn 
1 machines listed.
$ journalctl -M rkt-e16bafd0-3b0b-4ade-b482-d6de42d35e8c
-- Logs begin at Fri 2016-02-26 12:57:14 GMT, end at Fri 2016-02-26 12:57:14 GMT. --
...```
## Menghentikan Kontainer. Karena penerapan rkt yang kami gunakan menggunakan systemd-nspawn sebagai alat yang mendasari untuk meluncurkan kontainer, oleh karena itu kami menggunakan systemd untuk menghentikan kontainer :
```$ machinectl list
MACHINE                                  CLASS     SERVICE
rkt-55cb3a96-8199-4d08-a998-713b631d3210 container nspawn 
1 machines listed.
$ machinectl kill rkt-55cb3a96-8199-4d08-a998-713b631d3210```
## Mengubah docker image. Alat appcast docker2aci dapat digunakan untuk mengunduh gambar Docker dan mengonversinya ke format ACI appc. Dapatkan alat ini di sini. dengan perintah :
```$ docker2aci docker://lukebond/demo-api```
## Menjalankan Image Docker Secara Langsung
```$ sudo rkt run --insecure-options=image docker://lukebond/demo-api```
## Image Discovery and Distribution
```$ curl -sL https://quay.io/coreos/etcd | grep meta | grep discovery
  <meta name="ac-discovery" content="quay.io https://quay.io/c1/aci/{name}/{version}/{ext}/{os}/{arch}/">
  <meta name="ac-discovery-pubkeys" content="quay.io https://quay.io/aci-signing-key">```