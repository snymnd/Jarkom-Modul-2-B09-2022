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





## No 1
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

## No 2
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


## No 3
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

## Soal 4
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

## No 5
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













