# Instalasi RKT Container #
Ada tiga macam cara melakukan instalasi RKT container berdasarkan sistem operasi yang akan digunakan yaitu :
* Pada CoreOS Container Linux terdapat paket rkt yang sudah terinstal dan terkonfigurasi. Sehingga dapat langsung dijalankan. 
* Pada Linux distribution lainnya, perlu menginstal dan mengkonfiguarsi binary rkt dari paket linux tersebut. [https://coreos.com/rkt/docs/latest/distributions.html]url
* Pada Mac atau Windows dapat menggunakan Vagrant virtual machine untuk menjalankan rkt.

## Menginstal rkt pada Linux ##
Untuk menjalankan binary rkt terbaru pada kernel Linux amd64 dapat menjalankan perintah berikut :
```
wget https://github.com/rkt/rkt/releases/download/v1.29.0/rkt-v1.29.0.tar.gz
tar xzvf rkt-v1.29.0.tar.gz
cd rkt-v1.29.0
./rkt help
```
## Menggunakan Vagrant pada Mac dan Windows untuk menginstal rkt ##
Untuk Mac atau pengguna Vagrant lainnya perlu mengatur Vagrantfile, pastikan memiliki Vagrant 1.5.x ke atas yang sudah terinstal. Lalu unduh Vagrantfile dan jalankan Linux yang sudah terinstal rkt dengan perintah seperti berikut :
```
git clone https://github.com/rkt/rkt
cd rkt
vagrant up
```
Untuk menggunakan Vagrant pada Linux, biasanya perlu menggunakan libvirt sebagai VMM daripada VirtualBox. Maka perlu menginstal beberapa plugins yang diperlukan, convert the box, dan jalankan libvirt provider:
```
vagrant plugin install vagrant-libvirt
vagrant plugin install vagrant-mutate
vagrant mutate ubuntu/xenial64 libvirt
vagrant up --provider=libvirt
```

Mengakses Vagrant VM dan menjalankan rkt :
```
vagrant ssh
rkt --help
```
Setup Vagrant juga terdiri dari bash-completion untuk membantu perintah-perintah rkt dan pilihannya
```
vagrant ssh -c 'ip address'
...
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:04:e4:5d brd ff:ff:ff:ff:ff:ff
    inet 172.28.128.3/24 brd 172.28.128.255 scope global enp0s8
       valid_lft forever preferred_lft forever
...
```
Pada contoh, Vagrant memiliki IP address 172.28.128.3. Perintah selanjutnya yaitu untuk menjalankan container nginx untuk kemudahan host networking membuat pod langsung yang dapat diakses pada alamat jaringan dan port milih host.
```
sudo rkt run --net=host --insecure-options=image docker://nginx
```
Nginx container dapat diakses pada http://172.28.128.3. Untuk menggunakan container dengan default jaringan, perlu dilakukan route ke alamat jaringan 172.16.28.0/24 sehingga perlu dikonfigurasi seperti berikut :
Pada linux jalankan,
```
sudo ip route add 172.16.28.0/24 via 172.28.128.3
```
Pada Mac jalankan,
```
sudo route -n add 172.16.28.0/24 172.28.128.3
```
Sekarang nginx dapat digunakan untuk container jaringan default :
```
$ sudo rkt run --insecure-options=image docker://nginx
$ rkt list
UUID		APP	IMAGE NAME					STATE	CREATED		STARTED		NETWORKS
0c3ab969	nginx	registry-1.docker.io/library/nginx:latest	running	2 minutes ago	2 minutes ago	default:ip4=172.16.28.2
```
Pada contoh, IP container nginx adalah 172.16.28.2, sehingga host dapat diakases pada alamat http://172.16.28.2.