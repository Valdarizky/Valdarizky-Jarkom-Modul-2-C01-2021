# Valdarizky-Jarkom-Modul-2-C01-2021

# Anggota Kelompok
- **Muhammad Arifiansyah** (05111940000027)
- **Melanchthon Bonifacio Butarbutar** (05111940000097)
- **Muhammad Valda Rizky Nur Firdaus** (05111940000115)

# Pembahasan Soal

Script.sh

Foosa:
#!/bin/bash
apt-get update
apt-get upgrade
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.184.0.0/16
cat /etc/resolv.conf
echo nameserver 192.168.122.1 > /etc/resolv.conf


Lainnya:
#!/bin/bash
echo nameserver 192.168.122.1 > /etc/resolv.conf 
apt-get update
apt-get install bind9 -y
apt-get install dnsutils
~                             

## Soal 1-4
##EniesLobby

- Domain

vim /etc/bind/named.conf.local

isi:
zone "franky.c01.com" {
        type master;
        file "/etc/bind/kaizoku/franky.c01.com";
};


mkdir /etc/bind/kaizoku

cp /etc/bind/db.local /etc/bind/kaizoku/franky.c01.com

vim /etc/bind/kaizoku/franky.c01.com
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
-Reverse DNS

vim /etc/bind/named.conf.local 
isi:
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
vim /etc/bind/kaizoku/2.184.192.in-addr.arpa
isi:
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

### Alabasta & loguetown
vim /etc/resolv.conf
isi:
```
nameserver 192.184.2.2 #IP EniesLobby
```

host -t PTR 192.184.2.2
ping franky.c01.com.
ping www.franky.c01.com.
ping super.franky.c01.com.
ping www.super.franky.c01.com.




##Soal 5

###EniesLobby
vim /etc/bind/named.conf.local
isi:
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
### Water7
vim /etc/bind/named.conf.local
isi:
```
zone "franky.c01.com" {
    type slave;
    masters { 192.184.2.2; }; // Masukan IP EniesLobby tanpa tanda petik
    file "/var/lib/bind/franky.c01.com";
};
```

###Loguetown
vim /etc/resolv.conf
isi:
```
nameserver 192.184.2.2 #IP EniesLobby
nameserver 192.184.2.3 #IP Water7
#nameserver 192.168.122.1
```
**tulis di command line
ping franky.c01.com.
ping www.franky.c01.com.
ping super.franky.c01.com.
ping www.super.franky.c01.com.**


##Soal 6
##EniesLobby
vim /etc/bind/kaizoku/franky.c01.com
isi:
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
vim /etc/bind/named.conf.local
isi:
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
###Water7
vim /etc/bind/named.conf.options
Kemudian comment //dnssec-validation auto; dan tambahkan baris berikut pada /etc/bind/named.conf.options

allow-query{any;};

vim /etc/bind/named.conf.local
isi:
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

mkdir /etc/bind/sunnygo
cp /etc/bind/db.local /etc/bind/sunnygo/franky.c01.com
vim /etc/bind/sunnygo/franky.c01.com
isi:
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


###Loguetown
vim /etc/resolv.conf
isi:
nameserver 192.184.2.2 #IP EniesLobby
nameserver 192.184.2.3 #IP Water7
#nameserver 192.168.122.1

tulis di command line
ping mecha.franky.c01.com.
ping www.mecha.franky.c01.com.
```

##Soal 7
###water7
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


###Loguetown
tulis di command line
ping general.mecha.franky.c01.com.
ping www.general.mecha.franky.c01.com.





echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get install apache2 -y
apt-get install libapache2-mod-php7.0 -y
apt-get install php -y
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

cp franky.txt /etc/apache2/sites-available/franky.c01.com.conf
cp superfranky.txt /etc/apache2/sites-available/super.franky.c01.com.conf

mkdir /var/www/franky.c01.com
mkdir /var/www/super.franky.c01.com

cp /root/franky/home.html /var/www/franky.c01.com
cp /root/franky/index/php /var/www/franky.c01.com
cat htaccess.txt > /var/www/franky.c01.com/.htaccess

cp -r /root/super.franky/error /var/www/super.franky.c01.com
cp -r /root/super.franky/public /var/www/super.franky.c01.com
service apache2 start
```
##Soal 8-9
```
wget zip pada root, unzip dan remove zip

wget https://raw.githubusercontent.com/FeinardSlim/Praktikum-Modul-2-Jarkom/main/franky.zip

wget https://raw.githubusercontent.com/FeinardSlim/Praktikum-Modul-2-Jarkom/main/general.mecha.franky.zip

wget https://raw.githubusercontent.com/FeinardSlim/Praktikum-Modul-2-Jarkom/main/super.franky.zip

unzip franky.zip
rm -r franky.zip

unzip general.mecha.franky.zip
rm - r general.mecha.franky.zip

unzip super.franky.zip
rm -r super.franky.zip

cd /etc/apache2/sites-available
```

**copy file dari default ke file baru franky.c01.com.conf
ganti DocumentRoot, ServerName dan ServerAlias dan tambahkan tulisan yang BOLD**
```
<VirtualHost *:80>
        # The ServerName directive sets the request scheme, hostname and port that
        # the server uses to identify itself. This is used when creating
        # redirection URLs. In the context of virtual hosts, the ServerName
        # specifies what hostname must appear in the request's Host: header to
        # match this virtual host. For the default virtual host (this file) this
        # value is not decisive as it is used as a last resort host regardless.
        # However, you must set it for any further virtual host explicitly.
        #ServerName www.example.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/franky.c01.com
        ServerName franky.c01.com
        ServerAlias www.franky.c01.com

         <Directory /var/www/franky.c01.com>
                 Options +FollowSymLinks -Multiviews
                 AllowOverride All
         </Directory>

        # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular
        # modules, e.g.
        #LogLevel info ssl:warn

                ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        # For most configuration files from conf-available/, which are
        # enabled or disabled at a global level, it is possible to
        # include a line for only one particular virtual host. For example the
        # following line enables the CGI configuration for this host only
        # after it has been globally disabled with "a2disconf".
        #Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```

**Masuk ke /var/www/
mkdir franky.c01.com
vim .htaccess
isi sebagai berikut untuk rewrite**

RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^home$ index.php/home

kemudian a2enmod untuk enable modif
terakhir copy file dari folder franky ke /var/www/…

cp /root/franky/home.html /var/www/franky.c01.com
cp /root/franky/index/php /var/www/franky.c01.com
service apache2 restart
Nomor 10-13

**cd /etc/apache2/sites-available
copy file dari default ke file baru super.franky.c01.com.conf
ganti DocumentRoot, ServerName dan ServerAlias dan tambahkan tulisan yang BOLD untuk directory listing, alias dan error messagenya**
```
<VirtualHost *:80>
        # The ServerName directive sets the request scheme, hostname and port that
        # the server uses to identify itself. This is used when creating
        # redirection URLs. In the context of virtual hosts, the ServerName
        # specifies what hostname must appear in the request's Host: header to
        # match this virtual host. For the default virtual host (this file) this
        # value is not decisive as it is used as a last resort host regardless.
        # However, you must set it for any further virtual host explicitly.
        #ServerName www.example.com


         ServerAdmin webmaster@localhost
        DocumentRoot /var/www/super.franky.c01.com
        ServerName super.franky.c01.com
        ServerAlias www.super.franky.c01.com

        <Directory /var/www/super.franky.c01.com/public>
             Options +Indexes
        </Directory>

        <Directory /var/www/super.franky.c01.com/public/js>
                 Options +Indexes
        </Directory>
        ErrorDocument 404 /error/404.html
        Alias "/js" "/var/www/super.franky.c01.com/public/js"


        # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular
        # modules, e.g.
        #LogLevel info ssl:warn

                ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        # For most configuration files from conf-available/, which are
        # enabled or disabled at a global level, it is possible to
        # include a line for only one particular virtual host. For example the
        # following line enables the CGI configuration for this host only
        # after it has been globally disabled with "a2disconf".
        #Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```
**Masuk ke /var/www/
mkdir super.franky.c01.com
copy file dari folder yang diextract**
```
cp -r /root/super.franky/error /var/www/super.franky.c01.com
cp -r /root/super.franky/public /var/www/super.franky.c01.com
```
service apache2 restart

	
	

