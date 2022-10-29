# Jarkom-Modul-2-B09-2022

### Anggota Kelompok
| Nama                 | NRP        |
|----------------------|------------|
| Gery Febrian Setyara | 5025201151 |
| Muhammad Yunus       | 5025201171 |
| Hafiz Kurniawan      | 5025201032 |

## Persiapan
Topologi jaringan
![image](https://user-images.githubusercontent.com/99454377/198836282-31861e12-e96e-480e-825c-7e0e2d2c21d2.png)

#### Konfigurasi `Ostania`
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.177.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.177.2.1
	netmask 255.255.255.0


auto eth3
iface eth3 inet static
	address 192.177.3.1
	netmask 255.255.255.0
```

#### Konfigurasi `Wise`
```
auto eth0
iface eth0 inet static
	address 192.177.3.2
	netmask 255.255.255.0
	gateway 192.177.3.1
```

#### konfigurasi `SSS`
```
auto eth0
iface eth0 inet static
	address 192.177.1.2
	netmask 255.255.255.0
	gateway 192.177.1.1
```

#### Konfigurasi `Garden`
```
auto eth0
iface eth0 inet static
	address 192.177.1.3
	netmask 255.255.255.0
	gateway 192.177.1.1
 ```
 
 #### Konfigurasi `Berlint`
 ```
 auto eth0
iface eth0 inet static
	address 192.177.2.2
	netmask 255.255.255.0
	gateway 192.177.2.1
 ```
 
 #### Konfigurasi `Eden`
 ```
 auto eth0
iface eth0 inet static
	address 192.177.2.3
	netmask 255.255.255.0
	gateway 192.177.2.1
 ```





### No 1
Setelah melakukan konfigurasi pada topologi kita lakukan restart node pada topologi.
![image](https://user-images.githubusercontent.com/99454377/198836299-00dfc366-a40f-44b2-ae63-3996b26fc4d8.png)


Kemudian kita coba hubungkan dengan internet dengan 
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.177.0.0/16
```
![image](https://user-images.githubusercontent.com/99454377/198833185-9b4881bf-af59-4d0b-9bd2-3b486cc025f1.png)

##### Testing
kemudian kita coba apakah sudah terkoneksi dengan internet dengan mencoba ping ke `google.com`
![image](https://user-images.githubusercontent.com/99454377/198833476-45b29a39-1c4f-45d4-83eb-ee54ed763e1f.png)

### No 2
Membuat website utama `wise.b09.com` dengan alias `www.wise.b09.com`

Dimulai dengan membuat file direktori untuk menyimpan
`mkdir -p /etc/bind/wise`

Kemudian membuat website utama `wise.b09.com` dengan `NS` dan alias `www.wise.b09.com` dengan `CNAME`
```
mkdir -p /etc/bind/wise
cp /etc/bind/db.local /etc/bind/wise/wise.b09.com
echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     wise.b09.com. root.wise.b09.com. (
                     2022102501         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@               IN      NS      wise.b09.com.
@               IN      A       192.177.3.2     ; IP WISE
www             IN      CNAME   wise.b09.com.
```

#### Testing
test pada client `Garden`.
```
host -t A wise.b09.com
```
![image](https://user-images.githubusercontent.com/99454377/198835039-72a55aa2-6ec4-4553-a567-304448f74f5e.png)

test alias
```
host -t A www.wise.b09.com
```
![image](https://user-images.githubusercontent.com/99454377/198835117-78d8b47d-824d-4ba8-9acf-88ff2e5c902e.png)


### No 3
Membuat subdomain `eden.wise.b09.com` dan server alias `www.eden.wise.b09.com`

```
eden            IN      A       192.177.2.3     ; IP Eden
www.eden        IN      CNAME   eden.wise.b09.com.
```

#### Testing
test subdomain pada client `Garden`.
```
host -t A eden.wise.b09.com
```
![image](https://user-images.githubusercontent.com/99454377/198835404-320796ed-7fbf-4fd1-b9df-7070ed5e0873.png)


test alias subdomain pada client `Garden`.
```
host -t A www.eden.wise.b09.com
```
![image](https://user-images.githubusercontent.com/99454377/198835440-fe8bb9a3-1067-4eb2-aa27-7122a735976a.png)

### No 4
Membuat reverse domain untuk domain utama.
```
zone "3.177.192.in-addr.arpa" {
    // soal 4 reverse dns master
    type master;
    file "/etc/bind/wise/3.177.192.in-addr.arpa";
};
```
#### Testing
```
host -t PTR 192.177.3.2
```
![image](https://user-images.githubusercontent.com/99454377/198835787-914b452a-7a3b-4117-9b3b-1cb6317e0ea4.png)

### No 5
Membuat `berlint` sebagai DNS Slave

Membuat zone konfigurasi
```
echo '
zone "wise.b09.com" {
    type master;
    notify yes;
    also-notify {192.177.2.2 ; }; // IP Berlint
    allow-transfer { 192.177.2.2; }; // IP Berlint
    file "/etc/bind/wise/wise.b09.com";
};
```
#### Testing
Untuk mencoba apakah DNS Slave , kita matikan dahulu server 'Wise' dengan `service bind9 stop`
![image](https://user-images.githubusercontent.com/99454377/198836033-50b7c073-d999-49d7-8849-47bd8bbcd8c7.png)

Kemudian kita testing `ping wise.b09.com`

![image](https://user-images.githubusercontent.com/99454377/198836096-db6da20f-6e93-4bee-8c91-9f00aa66aa9b.png)

Masih terkoneksi ke internet sehingga DNS Slave berhasil.

### 6.  Karena banyak informasi dari Handler, buatlah subdomain yang khusus untuk operation yaitu operation.wise.yyy.com dengan alias www.operation.wise.yyy.com

- Buat directory ``operation`` pada folder `/etc/bind` dengan  
```bash 
mkdir /etc/bind/operation
```
- selanjutnya tambahkan configurasi berikut pada file `operation.wise.b09.com` folder yang telah dibuat.
![image](https://user-images.githubusercontent.com/70748569/198837494-b941e223-c4de-4e9d-ad7d-1bde804061f6.png)

- Pada wise tambahkan configrasi zone berikut ke ``/etc/bind/named.conf.local`` 
```bash
zone "operation.wise.b09.com" {
// delegasi dns ke berlint soal 6
    type master;
    allow-transfer { 192.177.2.2; }; // IP Berlint
    file "/etc/bind/operation/operation.wise.b09.com";
};
```
seperti berikut:
![image](https://user-images.githubusercontent.com/70748569/198837525-e6f25266-69f0-43f5-8fbf-a2cc11b42e70.png)


- Buat directory ``delegasi`` pada folder `/etc/bind` dengan  berlint
```bash 
mkdir /etc/bind/delegasi
```
- selanjutnya tambahkan configurasi berikut pada file `operation.wise.b09.com` folder yang telah dibuat untuk mendapatkal delegasi atas subdomain `operation` dan menambahkan alias `www.operation`.
![image](https://user-images.githubusercontent.com/70748569/198837542-79f22671-3633-4eb3-a4f6-3ffc1104b7e6.png)

- Lakukan configurasi berikut di `/etc/bind/named.conf.local` pada berlint
```bash 
zone "operation.wise.b09.com" {
// delegasi untuk no 6
    type master;
    file "/etc/bind/delegasi/operation.wise.b09.com";
};
``` 
- Berikut hasil testing pada SSS
![image](https://user-images.githubusercontent.com/70748569/198837562-d095a292-900e-4335-91a6-647ae0dffef6.png)


### 7. Untuk informasi yang lebih spesifik mengenai Operation Strix, buatlah subdomain melalui Berlint dengan akses strix.operation.wise.yyy.com dengan alias www.strix.operation.wise.yyy.com yang mengarah ke Eden
- Pada berlint, tambahkan konfigurasi berikut di `/etc/bind/delegasi/operation.wise.b09.com` untuk membuat subdomain `strix.operation` serta alias  `www.strix.operation`
![image](https://user-images.githubusercontent.com/70748569/198837584-d566a9ce-539f-4630-b063-36e27a7e61aa.png)

- berikut hasil testing pada SSS
![image](https://user-images.githubusercontent.com/70748569/198837725-89641abd-d35e-45e6-a78e-6662f90020a2.png)


### 8. Pertama dengan webserver www.wise.yyy.com. Pertama, Loid membutuhkan webserver dengan DocumentRoot pada /var/www/wise.yyy.com
Buat webserver pada wise, karena domain `wise.b09.com` mengarah ke IP wise.
- Install dependency yang dibutuhkan
```bash 
apt-get install apache2 -y
apt-get install php -y
apt-get install libapache2-mod-php7.0

apt-get install wget -y
apt-get install unzip -y
```
- Download resource wise yang ada pada soal shift dengan `wget` lalu unzip isi zip file dengan `unzip` lalu pindah hasil unzip kan pada folder berikuut `/var/www/wise.b09.com` seperti berikut
```bash
wget https://drive.google.com/uc?id=1S0XhL9ViYN7TyCj2W66BNEXQD2AAAw2e -O wise.zip
unzip wise.zip 

mv wise /var/www/wise.b09.com
```  
- Tambahkan configurasi berikut di `/etc/apache2/sites-available/wise.b09.com.conf` 
```bash 
<VirtualHost *:80>

        #ServerName www.example.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/wise.b09.com
        ServerName wise.b09.com
        ServerAlias www.wise.b09.com

        #LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
        #Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```
- Lalu jalankan  `a2ensite wise.b09.com` dan `service apache2 restart` untuk menjalankan webserver pada domain tersebut
- Berikut hasil testing pada sss yang sudah menginstall `lynx` dengan perintah ``lynx wise.b09.com``
![image](https://user-images.githubusercontent.com/70748569/198837759-a349850b-0d34-481b-bfac-9c6984bdcf28.png)


### 9 Setelah itu, Loid juga membutuhkan agar url www.wise.yyy.com/index.php/home dapat menjadi menjadi www.wise.yyy.com/home
- Buat alias dengan menambahakan code berikut pada  `/etc/apache2/sites-available/wise.b09.com.conf` 
```bash
<VirtualHost *:80>

        #ServerName www.example.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/wise.b09.com
        ServerName wise.b09.com
        ServerAlias www.wise.b09.com

        # no 9
        Alias "/home" "/var/www/wise.b09.com/index.php/home"
        #LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
        #Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```
- Testing pada SSS dengan menjalankan `lynx www.wise.b09.com/home`

![image](https://user-images.githubusercontent.com/70748569/198837791-7ff78a1e-b073-455e-8a01-3a96a8ee8d0b.png)

![image](https://user-images.githubusercontent.com/70748569/198837799-e84312a4-0467-4db2-9b97-beb4e87e9685.png)


### 10 pada subdomain www.eden.wise.yyy.com, Loid membutuhkan penyimpanan aset yang memiliki DocumentRoot pada /var/www/eden.wise.yyy.com

Buat webserver pada eden, karena domain `eden.wise.b09.com` mengarah ke IP eden.
- Install dependency yang dibutuhkan
```bash 
apt-get install apache2 -y
apt-get install php -y
apt-get install libapache2-mod-php7.0

apt-get install wget -y
apt-get install unzip -y
```
- Download resource wise yang ada pada soal shift dengan `wget` lalu unzip isi zip file dengan `unzip` lalu pindah hasil unzip kan pada folder berikuut `/var/www/wise.b09.com` seperti berikut
```bash
wget https://drive.google.com/uc?id=1q9g6nM85bW5T9f5yoyXtDqonUKKCHOTV -O eden.zip
unzip eden.zip 

mv eden.wise /var/www/eden.wise.b09.com
```  
- Tambahkan configurasi berikut di `/etc/apache2/sites-available/eden.wise.b09.com.conf` 
```bash 
<VirtualHost *:80>

        #ServerName www.example.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/eden.wise.b09.com
        ServerName eden.wise.b09.com
        ServerAlias eden.www.wise.b09.com
        
        #LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        #Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```
- Lalu jalankan  `a2ensite wise.b09.com` dan `service apache2 restart` untuk menjalankan webserver pada domain tersebut
- Berikut hasil testing pada sss dengan perintah 
-- saat menjalankan ``lynx eden.wise.b09.com``
![image](https://user-images.githubusercontent.com/70748569/198837822-466f72dd-3abe-484d-b616-7867de13d2fb.png)

-- saat menjalankan ``lynx www.eden.wise.b09.com``
![image](https://user-images.githubusercontent.com/70748569/198837827-ed0bb0a8-f2d1-4672-97bc-fa487222d223.png)

### 11. Akan tetapi, pada folder /public, Loid ingin hanya dapat melakukan directory listing saja

dalam soal nomor 11 ini diminta untuk melakukan directory listing pada directory public saja,directory listing dapat dilakukan dengan cara
```
<Directory /var/www/eden.wise.b09.com/public>
             Options +Indexes
         </Directory>
```
configurasi tersebut dalam eden.wise.b09.com.conf lalu jalankan lynx pada node SSS dengan
```
lynx eden.wise.b09.com/public
```
maka akan muncul hasil seperti ini

![11](https://user-images.githubusercontent.com/92217354/198834835-e7448a41-ae72-41a0-a9c1-5b8c6332cde6.jpeg)

### 12.Tidak hanya itu, Loid juga ingin menyiapkan error file 404.html pada folder /error untuk mengganti error kode pada apache

warning error yang biasanya hanya not found atau unable host saja (membosankan) maka dapat diganti dengan pesan text yang  berada pada 404.html yang didapat dari unzip dari file di google drive sebelumnya dengan menaruh configurasi ini dalam eden.wise.b09.com.conf
```
 ErrorDocument 404 /error/404.html
        ErrorDocument 500 /error/404.html
        ErrorDocument 502 /error/404.html
        ErrorDocument 503 /error/404.html
        ErrorDocument 504 /error/404.html
```
dan untuk pengujian dalam mencari lynx apapun maka akan muncul error yang tadinya membosankan jadi dapat di custom seperti

![image](https://user-images.githubusercontent.com/92217354/198835044-d3dc3244-5a92-4e3c-9c13-e82aac8dc475.png)

### 13.Loid juga meminta Franky untuk dibuatkan konfigurasi virtual host. Virtual host ini bertujuan untuk dapat mengakses file asset www.eden.wise.yyy.com/public/js menjadi www.eden.wise.yyy.com/js

Loid meminta agar yang tadinya url sangat panjang dan tidak efisien,dapat berubah menjadi singkat dan efisien dengan cara taruh berikut dalam eden.wise.b09.com.conf

```
 Alias "/js" "/var/www/eden.wise.b09.com/public/js"
```
agar ketika melakukan pengecekan directory listing tidak perlu mengetik terlalu panjang sehingga hanya perlu menuliskan `lynx eden.wise.b09.com/js` maka akan muncul tampilan seperti berikut 

![image](https://user-images.githubusercontent.com/92217354/198835225-9b892a81-875d-4bf5-878b-149976ed5ab4.png)

### 14.Loid meminta agar www.strix.operation.wise.yyy.com hanya bisa diakses dengan port 15000 dan port 15500

Loid meminta agar selain diakses oleh port 15000 dan 15500 maka tidak dapat mengakses sehingga memerlukan 
```
<VirtualHost *:15000>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/strix.operation.wise.b09.com
        ServerName strix.operation.wise.b09.com
        ServerAlias www.strix.operation.wise.b09.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```
dan
```
<VirtualHost *:15500>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/strix.operation.wise.b09.com
        ServerName strix.operation.wise.b09.com
        ServerAlias www.strix.operation.wise.b09.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```
mengatur port dari default 80 menjadi 15000 dan 15500 dan juga mengubah konfigurasi pada `ports.conf` pada /etc/apache2 
```
Listen 15000
Listen 15500

<IfModule ssl_module>
        Listen 443
</IfModule>

<IfModule mod_gnutls.c>
        Listen 443
</IfModule>
```
agar hanya dapat diakses oleh port 15000 dan 15500,untuk pengecekannya dapat memasukkan ping strix.operation.wise.b09.com atau IP Eden pada node SSS `lynx 192.177.2.3:15000` dan `lynx 192.177.2.3:15500` yang nantinya akan muncul tampilan seperti dibawah

![image](https://user-images.githubusercontent.com/92217354/198835456-59f95d9d-9a50-4f36-8dac-eb8ca35e6d04.png)

jika memasukkan selain kedua port tersebut maka akan muncul error yaitu cant reach host  











