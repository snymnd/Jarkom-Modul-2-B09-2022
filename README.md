# Jarkom-Modul-2-B09-2022

### Anggota Kelompok
| Nama                 | NRP        |
|----------------------|------------|
| Gery Febrian Setyara | 5025201151 |
| Muhammad Yunus       | 5025201171 |
| Hafiz Kurniawan      | 5025201032 |


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
agar hanya dapat diakses oleh port 15000 dan 15500,untuk pengecekannya dapat memasukkan ping strix.operation.wise.b09.com atau IP Eden `192.177.2.3:15000` dan `192.177.2.3:15500` yang nantinya akan muncul tampilan seperti dibawah

![image](https://user-images.githubusercontent.com/92217354/198835456-59f95d9d-9a50-4f36-8dac-eb8ca35e6d04.png)

jika memasukkan selain kedua port tersebut maka akan muncul error yaitu cant reach host  
