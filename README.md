# Valdarizky-Jarkom-Modul-2-C01-2021


# Anggota Kelompok
- **Muhammad Arifiansyah** (05111940000027)
- **Melanchthon Bonifacio Butarbutar** (05111940000097)
- **Muhammad Valda Rizky Nur Firdaus** (05111940000115)

# Soal
Luffy adalah seorang yang akan jadi Raja Bajak Laut. Demi membuat Luffy menjadi Raja Bajak Laut, Nami ingin membuat sebuah peta, bantu Nami untuk membuat peta berikut

![image](https://user-images.githubusercontent.com/81211647/139531843-69b94ae7-b163-4983-a1f6-560ab99a808e.png)

- EniesLobby akan dijadikan sebagai DNS Master, Water7 akan dijadikan DNS Slave, dan Skypie akan digunakan sebagai Web Server. Terdapat 2 Client yaitu Loguetown, dan Alabasta. Semua node terhubung pada router Foosha, sehingga dapat mengakses internet.
- Luffy ingin menghubungi Franky yang berada di EniesLobby dengan denden mushi. Kalian diminta Luffy untuk membuat website utama dengan mengakses franky.yyy.com dengan alias www.franky.yyy.com pada folder kaizoku. 
- Setelah itu buat subdomain super.franky.yyy.com dengan alias www.super.franky.yyy.com yang diatur DNS nya di EniesLobby dan mengarah ke Skypie. 
- Buat juga reverse domain untuk domain utama. 
- Supaya tetap bisa menghubungi Franky jika server EniesLobby rusak, maka buat Water7 sebagai DNS Slave untuk domain utama. 
- Setelah itu terdapat subdomain mecha.franky.yyy.com dengan alias www.mecha.franky.yyy.com yang didelegasikan dari EniesLobby ke Water7 dengan IP menuju ke Skypie dalam folder sunnygo. 
- Untuk memperlancar komunikasi Luffy dan rekannya, dibuatkan subdomain melalui Water7 dengan nama general.mecha.franky.yyy.com dengan alias www.general.mecha.franky.yyy.com yang mengarah ke Skypie. 
- Setelah melakukan konfigurasi server, maka dilakukan konfigurasi Webserver. Pertama dengan webserver www.franky.yyy.com. Pertama, luffy membutuhkan webserver dengan DocumentRoot pada /var/www/franky.yyy.com. 
- Setelah itu, Luffy juga membutuhkan agar url www.franky.yyy.com/index.php/home dapat menjadi menjadi www.franky.yyy.com/home.
- Setelah itu, pada subdomain www.super.franky.yyy.com, Luffy membutuhkan penyimpanan aset yang memiliki DocumentRoot pada /var/www/super.franky.yyy.com. 
- Akan tetapi, pada folder /public, Luffy ingin hanya dapat melakukan directory listing saja.
- Tidak hanya itu, Luffy juga menyiapkan error file 404.html pada folder /error untuk mengganti error kode pada apache . 
- Luffy juga meminta Nami untuk dibuatkan konfigurasi virtual host. Virtual host ini bertujuan untuk dapat mengakses file asset www.super.franky.yyy.com/public/js menjadi www.super.franky.yyy.com/js. 
- Dan Luffy meminta untuk web www.general.mecha.franky.yyy.com hanya bisa diakses dengan port 15000 dan port 15500 
- dengan autentikasi username luffy dan password onepiece dan file di /var/www/general.mecha.franky.yyy 
- Dan setiap kali mengakses IP Skypie akan dialihkan secara otomatis ke www.franky.yyy.com. 
- Dikarenakan Franky juga ingin mengajak temannya untuk dapat menghubunginya melalui website www.super.franky.yyy.com, dan dikarenakan pengunjung web server pasti akan bingung dengan randomnya images yang ada, maka Franky juga meminta untuk mengganti request gambar yang memiliki substring “franky” akan diarahkan menuju franky.png. Maka bantulah Luffy untuk membuat konfigurasi dns dan web server ini!


  
# Pembahasan Soal

isi Script.sh

Foosa:
```bash
#!/bin/bash
apt-get update
apt-get upgrade
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.184.0.0/16
cat /etc/resolv.conf
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

Lainnya:
```bash
#!/bin/bash
echo nameserver 192.168.122.1 > /etc/resolv.conf 
apt-get update
apt-get install bind9 -y
apt-get install dnsutils
```                             

## Soal 1
EniesLobby akan dijadikan sebagai DNS Master, Water7 akan dijadikan DNS Slave, dan Skypie akan digunakan sebagai Web Server. Terdapat 2 Client yaitu Loguetown, dan Alabasta. Semua node terhubung pada router Foosha, sehingga dapat mengakses internet (1).

Dilakukan konfigurasi terlebih dahulu seperti yang ada pada modul gns3
[Prefix IP]=192.184

### Foosha
```bash
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address [Prefix IP].1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address [Prefix IP].2.1
	netmask 255.255.255.0
```bash
### Loguetown
```bash
auto eth0
iface eth0 inet static
	address [Prefix IP].1.2
	netmask 255.255.255.0
	gateway [Prefix IP].1.1
```
### Alabasta
```bash
auto eth0
iface eth0 inet static
	address [Prefix IP].1.3
	netmask 255.255.255.0
	gateway [Prefix IP].1.1
```
### EniesLobby
```bash
auto eth0
iface eth0 inet static
	address [Prefix IP].2.2
	netmask 255.255.255.0
	gateway [Prefix IP].2.1
```
### Water7
```bash
auto eth0
iface eth0 inet static
	address [Prefix IP].2.3
	netmask 255.255.255.0
	gateway [Prefix IP].2.1
```

Ketikkan:
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s [Prefix IP].0.0/16 pada router Foosha<br>
```
Ketikkan:
```
command cat /etc/resolv.conf di Foosha<br>
```
Ketikkan:
```
echo nameserver 192.168.122.1 > /etc/resolv.conf. di semua node<br>
```
- Bukti SS:
![image](https://user-images.githubusercontent.com/81211647/139537286-c7e533fb-7fea-4f54-aa84-1d041f820cc2.png)


## Soal 2
##### ##EniesLobby

- buat dan buka folder konfigurasi

> vim /etc/bind/named.conf.local

isi:
```
zone "franky.c01.com" {
        type master;
        file "/etc/bind/kaizoku/franky.c01.com";
};
```

- buat dan buka folder:
```mkdir /etc/bind/kaizoku
cp /etc/bind/db.local /etc/bind/kaizoku/franky.c01.com
vim /etc/bind/kaizoku/franky.c01.com
```

isi:
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     franky.c01.com. root.franky.c01.com. (
                202110251               ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      franky.c01.com.
@       IN      A       192.184.2.2;IP EniesLobby
www     IN     CNAME    franky.c01.com.
super   IN      A       192.184.2.4;IP Skypie
www.super     IN   CNAME    super.franky.c01.com.
//@    		 IN        A   192.184.2.4;IP Skypie
//www.super	 IN	A    192.184.2.4;IP Skypie

~                                   

service bind9 restart
```
- Bukti SS:
![image](https://user-images.githubusercontent.com/81211647/139535288-4744fc7c-dd63-453a-899e-dc89f5f6a474.png)

## Soal 3
#### Setelah itu buat subdomain super.franky.yyy.com dengan alias www.super.franky.yyy.com yang diatur DNS nya di EniesLobby dan mengarah ke Skypie

tambahkan konfigurasi pada /etc/bind/kaizoku/franky.c01.com
```
www.super     IN   CNAME    super.franky.c01.com.
//@    		 IN        A   192.184.2.4;IP Skypie
```
-Bukti SS:
![image](https://user-images.githubusercontent.com/81211647/139537463-3421c869-48e9-4f42-bdf8-871c048deeb9.png)

## Soal 4

Buka file pada:
```
vim /etc/bind/named.conf.local 
```
Tambahkan konfigurasi seperti dibawah:
```
zone "franky.c01.com" {
        type master;
        file "/etc/bind/kaizoku/franky.c01.com";
};
zone "2.184.192.in-addr.arpa" {
    type master;
    file "/etc/bind/kaizoku/2.184.192.in-addr.arpa";
};

cp /etc/bind/db.local /etc/bind/kaizoku/2.184.192.in-addr.arpa
```
buat dan buka file:
```
vim /etc/bind/kaizoku/2.184.192.in-addr.arpa
```
Kemudian isi:
```;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     franky.c01.com. root.franky.c01.com. (
                202110251               ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
2.184.192.in-addr.arpa.       IN      NS      franky.c01.com.
2	IN	PTR	franky.c01.com.
```



service bind9 restart

##### ##Alabasta & loguetown
buka:
```
vim /etc/resolv.conf
```
testing:
```
nameserver 192.184.2.2 #IP EniesLobby
host -t PTR 192.184.2.2
ping franky.c01.com.
ping www.franky.c01.com.
ping super.franky.c01.com.
ping www.super.franky.c01.com.
```

- Bukti SS:
![image](https://user-images.githubusercontent.com/81211647/139535450-49740bca-4482-48b5-bc19-8452841ab908.png)



## Soal 5
#### Supaya tetap bisa menghubungi Franky jika server EniesLobby rusak, maka buat Water7 sebagai DNS Slave untuk domain utama
##### ##EniesLobby
- Tambahkan konfigurasi pada:
> vim /etc/bind/named.conf.local
isikan :
```zone "franky.c01.com" {
    type master;
    notify yes;
    also-notify { 192.184.2.3; }; // Masukan IP Water7 tanpa tanda petik
    allow-transfer { 192.184.2.3; }; // Masukan IP Water7 tanpa tanda petik
    file "/etc/bind/kaizoku/franky.c01.com";
};
zone "2.184.192.in-addr.arpa" {
    type master;
    file "/etc/bind/kaizoku/2.184.192.in-addr.arpa";
};

service bind9 restart
```
##### ##Water7
- pada Water 7 bukak dan tambahkan:
```
vim /etc/bind/named.conf.local
```
isikan:
```
zone "franky.c01.com" {
    type slave;
    masters { 192.184.2.2; }; // Masukan IP EniesLobby tanpa tanda petik
    file "/var/lib/bind/franky.c01.com";
};
```

##### ##Loguetown
vim /etc/resolv.conf
isi:
```
nameserver 192.184.2.2 #IP EniesLobby
nameserver 192.184.2.3 #IP Water7
#nameserver 192.168.122.1
```
- tulis di command line
```
ping franky.c01.com.
ping www.franky.c01.com.
ping super.franky.c01.com.
ping www.super.franky.c01.com.
```
-Bukti SS:
![image](https://user-images.githubusercontent.com/81211647/139535714-920c96df-833b-4a81-bf85-da7a20ca34aa.png)


## Soal 6
#### Setelah itu terdapat subdomain mecha.franky.yyy.com dengan alias www.mecha.franky.yyy.com yang didelegasikan dari EniesLobby ke Water7 dengan IP menuju ke Skypie dalam folder sunnygo

##### ##EniesLobby

- Buka file konfigurasi pada:
> vim /etc/bind/kaizoku/franky.c01.com
isikan:
```;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     franky.c01.com. root.franky.c01.com. (
                202110251               ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      franky.c01.com.
@       IN      A       192.184.2.2;IP EniesLobby
@       IN      A       192.184.2.4;IP Skypie
www     IN     CNAME    franky.c01.com.
super   IN      A       192.184.2.4;IP Skypie
www.super     IN  	A	 192.184.2.4;IP Skypie
ns1     IN      A       192.184.2.3;IP Water7
mecha   IN     NS       ns1

vim /etc/bind/named.conf.options
Kemudian comment dnssec-validation auto; dan tambahkan baris berikut pada /etc/bind/named.conf.options
allow-query{any;};
```
buka konfigurasi lagi pada:
> vim /etc/bind/named.conf.local
isikan:
```
zone "franky.c01.com" {
    type master;
    notify yes; 
    also-notify { 192.184.2.3; }; // Masukan IP Water7 tanpa tanda petik
    allow-transfer { 192.184.2.3; }; // Masukan IP Water7 tanpa tanda petik
    file "/etc/bind/kaizoku/franky.c01.com";
};
zone "2.184.192.in-addr.arpa" {
    type master;
    file "/etc/bind/kaizoku/2.184.192.in-addr.arpa";
};  

service bind9 restart
```
##### ##Water7
buka konfigurasi file:
> vim /etc/bind/named.conf.options

- Kemudian 
> comment //dnssec-validation auto;
- tambahkan baris berikut pada 
> /etc/bind/named.conf.options allow-query{any;};

- Buka konfigurasi file
> vim /etc/bind/named.conf.local
isikan:
```
zone "franky.c01.com" {
    type slave;
    masters { 192.184.2.2; }; // Masukan IP EniesLobby tanpa tanda petik
    file "/var/lib/bind/franky.c01.com";
};

zone "mecha.franky.c01.com" {
    type master;
    file "/etc/bind/sunnygo/franky.c01.com";
};
```
buat dan tambahkan direktori file:
```mkdir /etc/bind/sunnygo
cp /etc/bind/db.local /etc/bind/sunnygo/franky.c01.com
```
Lalu buka file konfigurasi:
> vim /etc/bind/sunnygo/franky.c01.com
isikan:
```;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     mecha.franky.c01.com. root.mecha.franky.c01.com. (
                202110251               ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      mecha.franky.c01.com.
@       IN      A       192.184.2.4;IP Skypie
www     IN     CNAME    mecha.franky.c01.com.

service bind9 restart
```

##### ##Loguetown
Buka file konfigurasi:
> vim /etc/resolv.conf
isikan:
```
nameserver 192.184.2.2 #IP EniesLobby
nameserver 192.184.2.3 #IP Water7
#nameserver 192.168.122.1
```
tulis di command line untuk melakukan ping
```
ping mecha.franky.c01.com.
ping www.mecha.franky.c01.com.
```
- Bukti SS:
![image](https://user-images.githubusercontent.com/81211647/139535548-4e1684ce-3d00-4e96-a16f-21d7d48031f6.png)


## Soal 7

#### Untuk memperlancar komunikasi Luffy dan rekannya, dibuatkan subdomain melalui Water7 dengan nama general.mecha.franky.yyy.com dengan alias www.general.mecha.franky.yyy.com yang mengarah ke Skypie

##### ##water7
- buka dan tambahkan konfigurasi pada 
> /etc/bind/sunnygo/mecha.franky.C01.com

isikan:
```
vim /etc/bind/sunnygo/franky.c01.com
isi:
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     mecha.franky.c01.com. root.mecha.franky.c01.com. (
                202110251               ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      mecha.franky.c01.com.
@       IN      A       192.184.2.4;IP Skypie
www     IN     CNAME    mecha.franky.c01.com.
general   IN      A       192.184.2.4;IP Skypie
www.general     IN   CNAME    general.mecha.franky.c01.com.

service bind9 restart
```

##### ##Loguetown
- Tulis di command line untuk testing dsb:
```
ping general.mecha.franky.c01.com.
ping www.general.mecha.franky.c01.com.

```
- Bukti SS:
![image](https://user-images.githubusercontent.com/81211647/139535636-fd869984-faf0-49af-9555-b4f706cdd7a7.png)

## Soal 8
#### Pada setiap soal setelah membuat file conf baru lakukan command a2ensite [Nama site]
Sebagai persiapan untuk soal 8-17 pertama jalankan command command berikut: <br>
```
apt-get install apache2 -y
apt-get install libapache2-mod-php7.0 -y
apt-get install php -y
apt-get install apache2 apache2-utils -y
apt-get install unzip -y
apt-get install wget -y
service apache2 start


wget https://raw.githubusercontent.com/FeinardSlim/Praktikum-Modul-2-Jarkom/main/franky.zip

wget https://raw.githubusercontent.com/FeinardSlim/Praktikum-Modul-2-Jarkom/main/general.mecha.franky.zip

wget https://raw.githubusercontent.com/FeinardSlim/Praktikum-Modul-2-Jarkom/main/super.franky.zip

unzip franky.zip
rm -r franky.zip

unzip general.mecha.franky.zip
rm - r general.mecha.franky.zip

unzip super.franky.zip
rm -r super.franky.zip

```

Command-command tersebut akan menginstall apache, php, wget, dan mendownload file dari soal. <br>

Untuk membuat web server pertama lakukan
```
cd /etc/apache2/sites-available
```

Kemudian copy template dari file 000-default ke file baru `cp 000-default.conf franky.C01.com.conf` <br>
Kemudian ubah DocumentRoot, nama dan alias:
```
 ServerAdmin webmaster@localhost
        DocumentRoot /var/www/franky.c01.com
        ServerName franky.c01.com
        ServerAlias www.franky.c01.com
```

Sebelumnya buatlah folder `franky.c01.com` pada path `/var/www` kemudian
copy file yand sebelumnya didownload ke folder tersebut
```
cp /root/franky/home.html /var/www/franky.c01.com
cp /root/franky/index/php /var/www/franky.c01.com
```
Jangan lupa restart apache2
```
service apache2 restart
```

Lakukan lynx pada Loguetown:
```
lynx www.franky.c01.com
```
Hasil: <br>

![ImgSrc](https://github.com/Valdarizky/Valdarizky-Jarkom-Modul-2-C01-2021/blob/main/images/soal81.png) <br>
![ImgSrc](https://github.com/Valdarizky/Valdarizky-Jarkom-Modul-2-C01-2021/blob/main/images/soal82.png)

## Soal 9

Tambahkan di `franky.c01.com.conf`
```
<Directory /var/www/franky.c01.com>
                 Options +FollowSymLinks -Multiviews
                 AllowOverride All
         </Directory>
```
Kemudian buat file `.htaccess` pada path `/var/www/franky.c01.com` yang isinya
```
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^home$ index.php/home
```
Dan aktifkan module rewrite dengan command `a2enmod rewrite`

Jangan lupa restart apache2
```
service apache2 restart
```
Lakukan lynx pada Loguetown:
```
lynx www.franky.c01.com/home
```
Hasil: <br>

![ImgSrc](https://github.com/Valdarizky/Valdarizky-Jarkom-Modul-2-C01-2021/blob/main/images/soal91.png) <br>
![ImgSrc](https://github.com/Valdarizky/Valdarizky-Jarkom-Modul-2-C01-2021/blob/main/images/soal82.png)

## Soal 10
Pertama copy file yang di download ke `/var/www/super.franky.c01.com`
```
cp -r /root/super.franky/error /var/www/super.franky.c01.com
cp -r /root/super.franky/public /var/www/super.franky.c01.com
```
Sama seperti pada nomor 9, pertama
```
cd /etc/apache2/sites-available
```

Kemudian `cp 000-default.conf super.franky.C01.com.conf`
Setelah itu ubah
```
 ServerAdmin webmaster@localhost
        DocumentRoot /var/www/super.franky.c01.com
        ServerName super.franky.c01.com
        ServerAlias www.super.franky.c01.com
```
Jangan lupa restart apache2
```
service apache2 restart
```

Lakukan lynx pada Loguetown:
```
lynx www.super.franky.c01.com
```
## Soal 11

Untuk directory listing tambahkan pada file conf:
```
<Directory /var/www/super.franky.c01.com/public>
             Options +Indexes
        </Directory>
```

Jangan lupa restart apache2
```
service apache2 restart
```

Lakukan lynx pada Loguetown:
```
lynx www.super.franky.c01.com/public
```
Hasil:

![ImgSrc](https://github.com/Valdarizky/Valdarizky-Jarkom-Modul-2-C01-2021/blob/main/images/soal112.png)
	
## Soal 12

Untuk soal ini cukup menambahkahkan line ini pada file conf:

```
        ErrorDocument 404 /error/404.html
```

Jangan lupa restart apache2
```
service apache2 restart
```
Kemudian lakukan lynx yang salah pada Loguetown 

```
lynx www.super.franky.c01.com/dwadawads
```
Hasil: <br>
![ImgSrc](https://github.com/Valdarizky/Valdarizky-Jarkom-Modul-2-C01-2021/blob/main/images/soal122.png)

## Soal 13

Untuk directory alias tambahkan pada file conf:
```
<Directory /var/www/super.franky.c01.com/public/js>
                 Options +Indexes
        </Directory>
        
        Alias "/js" "/var/www/super.franky.c01.com/public/js"
```
Jangan lupa restart apache2
```
service apache2 restart
```

Kemudian lakukan lynx pada Loguetown 

```
lynx www.super.franky.c01.com/js
```
Hasil: <br>

![ImgSrc](https://github.com/Valdarizky/Valdarizky-Jarkom-Modul-2-C01-2021/blob/main/images/soal132.png)

## Soal 14 
Pertama copy file yang telah di download ke folder
```
cp -r /root/general.mecha.franky/drag.wav /var/www/general.mecha.franky.c01.com/
cp -r /root/general.mecha.franky/duck-duck-go.jpg /var/www/general.mecha.franky.c01.com/
cp -r /root/general.mecha.franky/f1.png /var/www/general.mecha.franky.c01.com/
cp -r /root/general.mecha.franky/funko-pop.jpg /var/www/general.mecha.franky.c011
.com/
```

Kemudian buat file conf baru di `/etc/apache2/sites-available/` yaitu `general.mecha.franky.c01.com`

Sesuaikan `DocumentRoot`, `ServerName`, dan `ServerAlias`.

Kemudian ganti port di line pertama dari `80` menjadi `15000` dan tambahkan `15000` 
```
<VirtualHost *:15000 *:15500>
```

Di `/etc/apache2/ports.conf`  tambahkan 
```
Listen 15000
Listen 15500
```

Jangan lupa restart apache2
```
service apache2 restart
```

Kemudian lakukan lynx pada Loguetown 

```
lynx www.general.mecha.franky.c01.com:15000
```

Hasil: <br>


![ImgSrc](https://github.com/Valdarizky/Valdarizky-Jarkom-Modul-2-C01-2021/blob/main/images/soal142.png)

## Soal 15
Untuk mendapat akses ke `htpasswd` run command berikut
```
apt-get install apache2 apache2-utils -y
```

Untuk mengenerate file `.htpasswd` run command berikut
```
htpasswd -c /etc/apache2/.htpasswd luffy
```

Kemudian masukkan password `onepiece`

Pada file conf general franky tambahkan
```
<Directory "/var/www/general.mecha.franky.c01.com">
        AuthType Basic
        AuthName "Restricted Content"
        AuthUserFile /etc/apache2/.htpasswd
        Require valid-user
    </Directory>
    
```

Jangan lupa restart apache2
```
service apache2 restart
```


Kemudian lakukan lynx pada Loguetown 

```
lynx www.general.mecha.franky.c01.com:15000
```

Hasil: <br>

![ImgSrc](https://github.com/Valdarizky/Valdarizky-Jarkom-Modul-2-C01-2021/blob/main/images/soal152.png)

## Soal 16
Untuk soal ini saya tidak melakukan perubahan apa apa pada file, setelah di lynx langsung bisa.  
Lakukan
```
lynx 192.184.2.4
```

Hasil:  
![ImgSrc](https://github.com/Valdarizky/Valdarizky-Jarkom-Modul-2-C01-2021/blob/main/images/soal82.png)

## Soal 17
#### Soal ini masih belum bisa, file tidak terdownload
Tambahkan file `.htaccess` pada folder `/var/www/super.franky.c01.com`
Yang isinya
```
RewriteEngine On
RewriteRule (.*)franky(.*)(\.jpg|\.png|\.gif)$ http://super.franky.c01.com/publii
c/images/franky.png [L,R=301]
```
Kemudian pada conf super franky tambahkan
```
<Directory /var/www/super.franky.c01.com>
     Options +FollowSymLinks -Multiviews
     AllowOverride All
 </Directory>
 ```
 
 Hasil :
![ImgSrc](https://github.com/Valdarizky/Valdarizky-Jarkom-Modul-2-C01-2021/blob/main/images/soal152.png)

	

