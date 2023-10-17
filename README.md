# Jarkom-Modul2-IT18-2023

# lapres (Laporan Resmi)

    1. Keisya Nabila Zahra    (5027211058)
    2. Auditya Maulana Adnan  (5027211068)

# Laporan Resmi Praktikum 2 Jarkom

### Soal 1

Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian [sebagai berikut.](https://docs.google.com/spreadsheets/d/1OqwQblR_mXurPI4gEGqUe7v0LSr1yJViGVEzpMEm2e8/edit?usp=sharing) Folder topologi dapat diakses pada [drive berikut](https://drive.google.com/drive/folders/1Ij9J1HdIW4yyPEoDqU1kAwTn_iIxg3gk?usp=sharing)

![08](https://github.com/aud1tya4dnan/Jarkom-Modul2-IT18-2023/assets/107627453/d4602709-fdef-4208-b689-892133ebb3df)

---

untuk mengerjakan soal nomor 1,  pertama-tama kami mencoba membuat topologi sesuai dengan pembagian topologi yaitu yang kami dapatkan adalah topologi 8 seperti berikut.

![Untitled](https://github.com/aud1tya4dnan/Jarkom-Modul2-IT18-2023/assets/107627453/915ac8f7-c645-43bf-bc41-7c07dbb13b09)

setelah itu, kami membuat konfigurasi untuk masing masing node seperti berikut 

****************************************Router Configuration****************************************

```bash
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.242.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.242.2.1
	netmask 255.255.255.0
```

**********************Nakula Client Configuration**********************

```bash
auto eth0
iface eth0 inet static
	address 192.242.1.2
	netmask 255.255.255.0
	gateway 192.242.1.1
```

******************************************************Sadewa Client Configuration******************************************************

```bash
auto eth0
iface eth0 inet static
	address 192.242.1.3
	netmask 255.255.255.0
	gateway 192.242.1.1
```

********************************************Abimanyu Web Server Configuration********************************************

```bash
auto eth0
iface eth0 inet static
	address 192.242.1.4
	netmask 255.255.255.0
	gateway 192.242.1.1
```

************************************************************************Prabukusuma Web Server Configuration************************************************************************

```bash
auto eth0
iface eth0 inet static
	address 192.242.1.6
	netmask 255.255.255.0
	gateway 192.242.1.1
```

**************************************Arjuna Load Balancer Configuration**************************************

```bash
auto eth0
iface eth0 inet static
	address 192.242.2.2
	netmask 255.255.255.0
	gateway 192.242.2.1
```

********************************Werkudara DNS Slave Configuration********************************

```bash
auto eth0
iface eth0 inet static
	address 192.242.2.3
	netmask 255.255.255.0
	gateway 192.242.2.1
```

**********************************************************************Yudhistira DNS Master Configuration**********************************************************************

```bash
auto eth0
iface eth0 inet static
	address 192.242.2.4
	netmask 255.255.255.0
	gateway 192.242.2.1
```

Setelah membuat dan menetapkan seluruh konfigurasi dengan seluruh node, selanjutnya kami menginstall dependensi untuk masing-masing node. Agar tidak perlu menginstall ulang saat baru menyalakan GNS dan node-nodenya kami menyimpan command instalasinya di /.bashrc, untuk pembagian instalasinya seperti berikut.

**********************************Menyambungkan Seluruh Node dengan Internet********************************** 

pada router masukkan command seperti berikut.

```bash
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.242.0.0/16
```

selanjutnya pada seluruh node masukkan command seperti berikut 

```bash
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

setelah dapat mengakses internet, kita dapat melakukan instalasi dependesi yang di perlukan untuk masing-masing node, dan masukkan ke /.bashrc 

**************Client************** 

```bash
apt-get update         
apt-get install dnsutils
apt-get install mcedit 
```

******************************Yudhistira DNS Master****************************** 

```bash
apt-get update  
apt-get install bind9 -y
apt-get install mcedit
```

********Werkudara DNS Slave******** 

```bash
apt-get update  
apt-get install bind9 -y
apt-get install mcedit
```

**********************Web Server********************** 

```bash
apt-get update 
apt-get install nginx 
apt-get install mcedit
```

******************Arjuna Load Balancer****************** 

```bash
apt-get update 
apt-get install nginx
apt-get install mcedit
```

**************************************************************Apache untuk AbimanyuWebServer************************************************************** 

```bash
apt-get install apache2 -y
service apache2 start
apt-get install php -y
```

---

### Soal 2

Buatlah website utama pada node arjuna dengan akses ke **arjuna.yyy.com** dengan alias **www.arjuna.yyy.com** dengan yyy merupakan kode kelompok.

---

Pertama-tama bukalah YudhistiraDNSMaster lalu masukkan script berikut pada  `/etc/bind/named.conf.local`  

```bash
zone "arjuna.it18.com" {  
        type master;  
        file "/etc/bind/arjuna/arjuna.it18.com";
};
```

setelah itu, buatlah folder arjuna di `/etc/bind` dengan command berikut 

```bash
mkdir /etc/bind/arjuna
```

setelah itu editlah `/etc/bind/arjuna/arjuna.it18.com` dan masukkan script berikut 

```bash
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     arjuna.it18.com. root.arjuna.it18.com. (

                        2022100601      ; Serial
	
                        604800          ; Refresh

                        86400           ; Retry

                        2419200         ; Expire

                        604800 )        ; Negative Cache TTL

@       IN      NS      arjuna.it18.com.

@       IN      A       192.242.2.2      ; IP ArjunaLoadBalancer

www    IN     CNAME   arjuna.it18.com.
```

jangan lupa masukkan ip arjuna dan juga gunakkan CNAME untuk alias dengan www. Setelah itu, lakukan restart bind9 dengan command berikut 

```bash
service bind9 restart
```

Selanjutnya bukalah client, di contoh ini saya menggunakan node Nakula Client. Pada Client, editlah `/etc/resolv.conf` , comment lah IP nameserver 192.168.122.1 dan tambahkan nameserver 192.242.2.4 # IP YudhistiraDNSMaster serta nameserver 192.242.2.2 # IP ArjunaLoadBalancer. Sehingga isi `/etc/resolv.conf` menjadi seperti berikut.

```bash
#nameserver 192.168.122.1
nameserver 192.242.2.4 # IP YudhistiraDNSMaster
nameserver 192.242.2.2 # IP ArjunaLoadBalancer 
```

selanjutnya cobalah ping [arjuna.it18.com](http://arjuna.IT18.com) dan ping [www.arjuna.it18.com](http://www.arjuna.IT18.com) sehingga hasilnya seperti berikut 

![Untitled 1](https://github.com/aud1tya4dnan/Jarkom-Modul2-IT18-2023/assets/107627453/f00ae6e8-0c87-413e-a77b-9ef5c87cecbd)

---

### Soal 3

Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke **abimanyu.yyy.com** dan alias **www.abimanyu.yyy.com**.

---

Pertama-tama bukalah YudhistiraDNSMaster lalu tambahkan script berikut pada  `/etc/bind/named.conf.local`  

```bash
zone "abimanyu.it18.com" {
	type master;
	file "/etc/bind/abimanyu/abimanyu.it18.com";
};
```

setelah itu, buatlah folder abimanyu di `/etc/bind` dengan command berikut 

```bash
mkdir /etc/bind/abimanyu
```

setelah itu editlah `/etc/bind/abimanyu/abimanyu.it18.com` dan masukkan script berikut 

```bash
;
; BIND data file for local loopback interface

;
$TTL    604800
@       IN      SOA     abimanyu.it18.com. root.abimanyu.it18.com. (

                        2022100601      ; Serial
	
                        604800          ; Refresh

                        86400           ; Retry\

                        2419200         ; Expire

                        604800 )        ; Negative Cache TTL

@       IN      NS      abimanyu.it18.com.

@       IN      A       192.242.1.4      ; IP AbimanyuWebServer

www    IN     CNAME   abimanyu.it18.com.
```

jangan lupa masukkan ip arjuna dan juga gunakkan CNAME untuk alias dengan www. Setelah itu, lakukan restart bind9 dengan command berikut 

```bash
service bind9 restart
```

Selanjutnya bukalah client, di contoh ini saya menggunakan node Nakula Client. Pada Client, editlah `/etc/resolv.conf` , comment lah IP nameserver 192.168.122.1 dan tambahkan nameserver 192.242.2.4 # IP YudhistiraDNSMaster serta nameserver 192.242.1.4 # IP AbimanyuWebServer  . Sehingga isi `/etc/resolv.conf` menjadi seperti berikut.

```bash
#nameserver 192.168.122.1
nameserver 192.242.2.4 # IP YudhistiraDNSMaster
nameserver 192.242.2.2 # IP ArjunaLoadBalancer 
nameserver 192.242.1.4 # IP AbimanyuWebServer 
```

selanjutnya cobalah ping [abimanyu.it18.com](http://arjuna.IT18.com) dan ping [www.abimanyu.it18.com](http://www.arjuna.IT18.com) sehingga hasilnya seperti berikut 

![Untitled 2](https://github.com/aud1tya4dnan/Jarkom-Modul2-IT18-2023/assets/107627453/b6f0b57a-2838-4112-9a78-5c73432b1dc0)


---

### Soal 4

Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain **parikesit.abimanyu.yyy.com** yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.

---

Bukalah YudhistiraDNSMaster, dan kemudian editlah `/etc/bind/abimanyu/abimanyu.it18.com` menjadi seperti berikut 

```bash
;
; BIND data file for local loopback interface

;
$TTL    604800
@       IN      SOA     abimanyu.it18.com. root.abimanyu.it18.com. (

                        2022100601      ; Serial
	
                        604800          ; Refresh

                        86400           ; Retry\

                        2419200         ; Expire

                        604800 )        ; Negative Cache TTL

@       IN      NS      abimanyu.it18.com.

@       IN      A       192.242.1.4      ; IP AbimanyuWebServer

www    IN     CNAME   abimanyu.it18.com

parikesit  IN  A       192.242.1.4        ; IP AbimanyuWebServer

www.parikesit IN  CNAME    parikesit.abimanyu.it18.com.
```

jangan lupa masukkan IP untuk subdomain parikesit dan gunakan CNAME untuk alias dari parikesit.abimanyu.it18.com

angan lupa masukkan ip arjuna dan juga gunakkan CNAME untuk alias dengan www. Setelah itu, lakukan restart bind9 dengan command berikut 

```bash
service bind9 restart
```

Selanjutnya bukalah client, di contoh ini saya menggunakan node Nakula Client. Pada Client, editlah `/etc/resolv.conf` , comment lah IP nameserver 192.168.122.1 dan tambahkan nameserver 192.242.2.4 # IP YudhistiraDNSMaster serta nameserver 192.242.1.4 # IP AbimanyuWebServer. Sehingga isi `/etc/resolv.conf` menjadi seperti berikut.

```bash
#nameserver 192.168.122.1
nameserver 192.242.2.4 # IP YudhistiraDNSMaster
nameserver 192.242.2.2 # IP ArjunaLoadBalancer 
nameserver 192.242.1.4 # IP AbimanyuWebServer 
```

selanjutnya cobalah ping parikesit.[abimanyu.it18.com](http://arjuna.IT18.com) dan ping [www.parikesit.abimanyu.it18.com](http://www.arjuna.IT18.com) sehingga hasilnya seperti berikut

![Untitled 3](https://github.com/aud1tya4dnan/Jarkom-Modul2-IT18-2023/assets/107627453/294ee334-b3dd-4847-bbb8-91d6bd8cc8a1)


---

**************Soal 5************** 

Buat juga reverse domain untuk domain utama. (*Abimanyu saja yang direverse*)

---

Pertama-tama bukalah YudhistiraDNSMaster lalu tambahkan script berikut pada  `/etc/bind/named.conf.local`  

```bash
zone "1.242.192.in-addr.arpa" {
    type master;
    file "/etc/bind/abimanyu/1.242.192.in-addr.arpa";
};
```

setelah itu editlah `/etc/bind/abimanyu/1.242.192.in-addr.arpa` dan masukkan script berikut 

```bash
;
; BIND data file for local loopback interface
;
$TTL    604800  
@       IN      SOA     abimanyu.it18.com. root.abimanyu.it18.com. (
                        2021100401      ; Serial
                        604800          ; Refresh
                        86400         ; Retry
                        2419200         ; Expire
                        604800 )       ; Negative Cache TTL
;
@   		IN      NS      abimanyu.it18.com.
1.242.192.in-addr.arpa	IN	NS	abimanyu.it18.com.
4                       IN      PTR     abimanyu.it18.com.
```

Kemudian cobalah cek dengan command `host -t PTR 192.242.1.4` pada client seperti berikut 

![Untitled 4](https://github.com/aud1tya4dnan/Jarkom-Modul2-IT18-2023/assets/107627453/2d0b6020-3128-45fd-b6c6-cb5397fa5081)

---

### Soal 6

Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.

---

Pertama-tama bukalah YudhistiraDNSMaster lalu edit script abimanyu berikut pada  `/etc/bind/named.conf.local`  menjadi seperti berikut 

```bash
zone "abimanyu.it18.com" {
    type master;
    also-notify  {192.242.2.3;};  //Masukan IP Wekudara tanpa tanda petik
    allow-transfer {192.242.2.3;};.
    file "/etc/bind/abimanyu/abimanyu.it18.com";
};
```

kemudian lakukan bind9 restart dengan command berikut 

```bash
service bind9 restart
```

Lalu bukalah WerkudaraDNSSlave dan tambahkan script  berikut pada  `/etc/bind/named.conf.local` 

```bash
zone "abimanyu.it18.com" {
        type slave;
        masters { 192.242.2.4; }; // Masukan IP master tanpa tanda petik
        file "/var/lib/bind/abimanyu.it18.com";
};

zone "arjuna.it18.com" {
        type slave;
        masters { 192.242.2.4; }; // Masukan IP master tanpa tanda petik
        file "/var/lib/bind/arjuna.it18.com";
};
```

kemudian lakukan bind9 restart dengan command berikut 

```bash
service bind9 restart
```

kemudian untuk testing, matikanlah bind9 pada YudhistiraDNSMaster dengan command berikut 

```bash
service bind9 stop
```

kemudian bukalah client, dan editlah `/etc/resolv.conf` menjadi seperti berikut 

```bash
#nameserver 192.168.122.1
nameserver 192.242.2.4 # IP YudhistiraDNSMaster
nameserver 192.242.2.3 # IP WerkudaraDNSSlave
#nameserver 192.242.2.2 # IP ArjunaLoadBalancer 
#nameserver 192.242.1.4 # IP AbimanyuWebServer
```

Lalu, cobalah untuk ping [arjuna.it18.com](http://arjuna.it18.com), [www.arjuna.it18.com](http://www.arjuna.it18.com), [abimanyu.it18.com](http://abimanyu.it18.com) dan [www.abimanyu.it18.com](http://www.abimanyu.it18.com) seperti berikut 

![Untitled 5](https://github.com/aud1tya4dnan/Jarkom-Modul2-IT18-2023/assets/107627453/2ffbf650-a040-477e-b7cf-67aed284e3cf)

![Untitled 6](https://github.com/aud1tya4dnan/Jarkom-Modul2-IT18-2023/assets/107627453/348b5160-c9aa-41ea-8f2d-5c8ab06bd520)


---

### Soal 7

Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu **baratayuda.abimanyu.yyy.com** dengan alias **www.baratayuda.abimanyu.yyy.com** yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.

---

Pertama-tama bukalah YudhistiraDNSMaster lalu edit script di  `/etc/bind/abimanyu/abimanyu.it18.com`  seperti berikut 

```bash
;
; BIND data file for local loopback interface

;
$TTL    604800
@       IN      SOA     abimanyu.it18.com. root.abimanyu.it18.com. (

                        2022100601      ; Serial
	
                        604800          ; Refresh

                        86400           ; Retry\

                        2419200         ; Expire

                        604800 )        ; Negative Cache TTL

@       IN      NS      abimanyu.it18.com.

@       IN      A       192.242.1.4      ; IP AbimanyuWebServer

www    IN     CNAME   abimanyu.it18.com

parikesit  IN  A       192.242.1.4        ; IP AbimanyuWebServer

www.parikesit IN  CNAME    parikesit.abimanyu.it18.com.

ns1           IN   A       192.242.2.3 ; IP WerkudaraDNSSlave

baratayuda    IN   NS      ns1
```

Kemudian, bukalah `/etc/bind/named.conf.options` dengan melakukan comment pada `dnssec-validation auto;` dan tambahkan `allow-query{any;};` pada baris selanjutnya . Selanjutnya, edit script abimanyu berikut pada  `/etc/bind/named.conf.local`  menjadi seperti berikut 

```bash
zone "abimanyu.it18.com" {
    type master;
    //also-notify  {192.242.2.3;};  //Masukan IP Wekudara tanpa tanda petik
    allow-transfer {192.242.2.3;};.
    file "/etc/bind/abimanyu/abimanyu.it18.com";
};
```

kemudian lakukan bind9 restart dengan command berikut 

```bash
service bind9 restart
```

Kemudian, bukalah `/etc/bind/named.conf.options` dengan melakukan comment pada `dnssec-validation auto;` dan tambahkan `allow-query{any;};` pada baris selanjutnya. Selanjutnya edit lah `/etc/bind/named.conf.local` dan tambahkan script berikut 

```bash
zone "baratayuda.abimanyu.it18.com"{..

        type master;

        file "/etc/bind/baratayuda/baratayuda.abimanyu.it18.com";

};
```

kemudian buatlah direktori baratayuda di `/etc/bind` dengan command berikut 

```bash
mkdir /etc/bind/baratayuda/
```

kemudian tambahkan script berikut di `/etc/bind/baratayuda/baratayuda.abimanyu.it18.com` 

```bash
$TTL    604800

@       IN      SOA     baratayuda.abimanyu.it18.com. root.baratayuda.abimanyu.it18.com. (

                        2022100601      ; Serial

                        604800         ; Refresh

                        86400         ; Retry

                        2419200         ; Expire

                        604800 )       ; Negative Cache TTL
;
@               IN      NS      baratayuda.abimanyu.it18.com.

@               IN      A       192.242.1.4       ;ip abimanyu
www             IN      CNAME   baratayuda.abimanyu.it18.com.
```

kemudian restartlah bind9 seperti berikut 

```bash
service bind9 restart
```

Lalu, bukalah client, dan editlah `/etc/resolv.conf` menjadi seperti berikut 

```bash
#nameserver 192.168.122.1
nameserver 192.242.2.4 # IP YudhistiraDNSMaster
nameserver 192.242.2.3 # IP WerkudaraDNSSlave
#nameserver 192.242.2.2 # IP ArjunaLoadBalancer 
#nameserver 192.242.1.4 # IP AbimanyuWebServer
```

kemudian cobalah testing di client dengan ping [baratayuda.abimanyu.it18.com](http://baratayuda.abimanyu.it18.com) dan ping [www.baratayuda.it18.com](http://www.baratayuda.it18.com) seperti berikut 

![Untitled 7](https://github.com/aud1tya4dnan/Jarkom-Modul2-IT18-2023/assets/107627453/063da929-6fe7-49d4-b991-7a9882fbb81e)

---

### Soal 8

Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses **rjp.baratayuda.abimanyu.yyy.com** dengan alias **www.rjp.baratayuda.abimanyu.yyy.com** yang mengarah ke Abimanyu.

---

Bukalah DNS Slave dan editlah file `/etc/bind/baratayuda/baratayuda.abimanyu.it18.com` menjadi seperti berikut 

```bash
$TTL    604800

@       IN      SOA     baratayuda.abimanyu.it18.com. root.baratayuda.abimanyu.it18.com. (

                        2022100601      ; Serial

                        604800         ; Refresh

                        86400         ; Retry

                        2419200         ; Expire

                        604800 )       ; Negative Cache TTL
;
@               IN      NS      baratayuda.abimanyu.it18.com.

@               IN      A       192.242.1.4       ;ip abimanyu

www             IN      CNAME   baratayuda.abimanyu.it18.com.

rjp         IN      A       192.242.1.4       ;ip abimanyu

www.rjp     IN      CNAME   baratayuda.abimanyu.it18.com.
```

kemudian restartlah bind9 seperti berikut 

```bash
service bind9 restart
```

Lalu, bukalah client, dan editlah `/etc/resolv.conf` menjadi seperti berikut 

```bash
#nameserver 192.168.122.1
nameserver 192.242.2.4 # IP YudhistiraDNSMaster
nameserver 192.242.2.3 # IP WerkudaraDNSSlave
#nameserver 192.242.2.2 # IP ArjunaLoadBalancer 
#nameserver 192.242.1.4 # IP AbimanyuWebServer
```

Kemudian di client cobalah testing dengan ping [rjp.baratayuda.abimanyu.it18.com](http://rjp.baratayuda.abimanyu.it.com) dan [www.rjp.baratayuda.abimanyu.it18.com](http://www.rjp.baratayuda.abimanyu.it18.com) seperti berikut

![Untitled 8](https://github.com/aud1tya4dnan/Jarkom-Modul2-IT18-2023/assets/107627453/1fae06ac-b444-4c2b-b3c3-92892af963c8)

---

### Soal 9

Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker

---

Bukalah ArjunaLoadBalancer dan lakukan start nginx seperti berikut 

```bash
service nginx start
```

Kemudian pada setiap node webserver lakukan start nginx seperti berikut

```bash
service nginx start
```

Kemudian lakukan checking dan pastikan nginx pada seluruh webserver dan ArjunaLoadBalancer sudah online. Lalu lakukan testing pada webserver dengan command `curl -sSf [http://localhost](http://localhost)` dan didapatkan hasil seperti berikut. 

![Untitled 9](https://github.com/aud1tya4dnan/Jarkom-Modul2-IT18-2023/assets/107627453/a8cf8759-92a4-4558-b045-fc7377f68189)

---

### Soal 10

1. Kemudian gunakan algoritma **Round Robin** untuk Load Balancer pada **Arjuna**. Gunakan *server_name* pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh

- *Prabakusuma:8001*

- *Abimanyu:8002*

-*Wisanggeni:8003*

---

Bukalah ArjunaLoadBalancer dan editlah  `/etc/nginx/sites-available/default` dengan script berikut 

```bash
upstream backend {
    server 192.242.1.4:8001;
    server 192.242.1.5:8002;
    server 192.242.1.6:8003;
}

server {
    listen 80;
    server_name arjuna;

    location / {
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

lakukan restart nginx dengan command berikut 

```bash
service nginx restart
```

Kemudian akseslah ketiga webserver dan ubah pada  `/etc/nginx/sites-available/default` di masing-masing webserver seperti berikut 

**********Abimanyu**********

```bash
server {
   		 listen 8001;
   		 server_name abimanyu;
    		# Konfigurasi situs web Abimanyu default nya
   		 …
}
```

************************Prabukusuma************************

```bash
server {
    listen 8002;
    server_name prabakusuma;
    # Konfigurasi situs web Prabakusuma default nya
    ...
}
```

**********Wisanggeni**********

```bash
ubah pada wisanggeni /etc/nginx/sites-available/default
server {
    listen 8003;
    server_name wisanggeni;
    # Konfigurasi situs web wisanggeni default nya
    ...
}
```

kemudian pada ketiga webserver lakukan restart nginx dengan command seperti berikut 

```bash
service nginx restart
```

lalu lakukan testing dengan command `lynx 192.242.2.2` secara 3 kali  seperti berikut 

<img width="389" alt="Untitled 10" src="https://github.com/aud1tya4dnan/Jarkom-Modul2-IT18-2023/assets/107627453/14e44037-f288-49a4-bb6b-cdf64a5ee10f">

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2011.png)

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2012.png)

---

### Soal 11

Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server **www.abimanyu.yyy.com**. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy

---

Pertama-tama bukalah AbimanyuWebServer dan cobalah restart apache 

```bash
service apache2 restart
```

tambahkan script dibawah pada `/etc/apache2/sites-available/abimanyu.yyy.com.conf` seperti berikut 

```bash
<VirtualHost *:80>
    ServerName www.abimanyu.it18.com
    DocumentRoot /var/www/abimanyu.it18
    <Directory /var/www/abimanyu.it18>
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

lalu buatlah direktori `abimanyuit18` pada `/var/www` seperti berikut 

```bash
mkdir /var/www/abimanyu.it18
```

lalu kita ubah kepemilikan dengan chown dengan command berikut 

```bash
chown -R www-data:www-data /var/www/abimanyu.it18
```

kemudian kita menggunakan a2ensite untuk seperti berikut 

```bash
a2ensite abimanyu.it18.com.conf
```

lalu kita aktifkan module rewrite dengan command berikut 

```bash
a2enmod rewrite
```

kemudian kita restart apache dengan command berikut 

```bash
service apache2 restart
```

untuk testing, kita lakukan di client dan kita lakukan command `curl -sSf [www.abimanyu.it18.com](http://www.abimanyu.it18.com/)` dan didapatkan hasil seperti berikut 

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2013.png)

---

### Soal 12

Setelah itu ubahlah agar url **www.abimanyu.yyy.com/index.php/home** menjadi **www.abimanyu.yyy.com/home.**

---

bukalah AbimanyuWebServer dan editlah `/var/www/abimayu.it18/.htaccess` dengan script berikut 

```bash
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule (.*) /index.php/\$1 [L]
```

kemudian kita restart apache dengan command berikut 

```bash
service apache2 restart
```

untuk testing, kita lakukan di client dengan menggunakan command `lynx [www.abimanyu.it18.com/index/php/home](http://www.abimanyu.it18.com/index/php/home)` dan didapatkan hasil seperti berikut 

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2014.png)

kemudian selanjutnya testing untuk `lynx [www.abimanyu.it18.com/home](http://www.abimanyu.it18.com/home)` dan didapatkan hasil seperti berikut 

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2015.png)

---

### Soal 13

Selain itu, pada subdomain **www.parikesit.abimanyu.yyy.com,** DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy

---

Bukalah AbimanyuWebServer dan tambahkan script berikut pada `/etc/apache2/sites-available/**parikesit.abimanyu.it18.com.conf`** 

```bash
<VirtualHost *:80>
    ServerName www.parikesit.abimanyu.it18.com
    DocumentRoot /var/www/parikesit.abimanyu.it18
    <Directory /var/www/parikesit.abimanyu.it18/public>
        Options +Indexes
        AllowOverride None
        Require all granted
    </Directory>
    <Directory /var/www/parikesit.abimanyu.it18/secret>
        Options -Indexes
        AllowOverride None
        Require all denied
    </Directory>
    ErrorDocument 404 /error/404.html
    ErrorDocument 403 /error/403.html
</VirtualHost>
```

lalu buatlah direktori `parikesit.abimanyu.it18` pada `/var/www` dengan command berikut 

```bash
mkdir /var/www/parikesit.abimanyu.it18
```

Lalu lakukan copy data dari resource soal dan ubahlah kepemilikan dengan chown seperti command berikut 

```bash
chown -R www-data:www-data /var/www/abimanyu.it18
```

Kemudian gunakan a2ensite untuk mengaktifkan (*ENABLE*) konfigurasi website yang telah dibuat seperti berikut 

```bash
a2ensite parikesit.abimanyu.it18.com.conf
```

lalu lakukan apache restart dengan command berikut 

```bash
service apache restart
```

Untuk testing, lakukan testing dengan command `lynx [www.parikesit.abimanyu.it18.com](http://www.parikesit.abimanyu.it18.com/)` dan didapatkan hasil seperti berikut 

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2016.png)

---

### Soal 14

Pada subdomain tersebut folder /public hanya dapat melakukan *directory listing* sedangkan pada folder /secret tidak dapat diakses *(403 Forbidden)*.

---

bukalah AbimanyuWebServer dan ubah script [parikesit.abimanyu.it18.com](http://parikesit.abimanyu.it18.com/).conf  dengan script berikut 

```bash
echo -e '<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/parikesit.abimanyu.it18
  ServerName parikesit.abimanyu.it18.com
  ServerAlias www.parikesit.abimanyu.it18.com

  <Directory /var/www/parikesit.abimanyu.it18/public>
          Options +Indexes
  </Directory>

  <Directory /var/www/parikesit.abimanyu.it18/secret>
          Options -Indexes
  </Directory>

  Alias "/public" "/var/www/parikesit.abimanyu.it18/public"
  Alias "/secret" "/var/www/parikesit.abimanyu.it18/secret"

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.it18.com.conf
```

lakukan restart apache dengan command berikut 

```bash
service apache2 restart
```

Selanjutnya, lakukan testing pada client dengan command `lynx [parikesit.abimanyu.it18.com/public](http://parikesit.abimanyu.it18.com/public)`  dan didapatkan hasil seperti berikut 

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2017.png)

Lakukan juga testing pada client dengan command `lynx [parikesit.abimanyu.it18.com/secret](http://parikesit.abimanyu.it18.com/secret)` sehingga di dapatkan hasil seperti berikut 

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2018.png)

---

### Soal 15

Buatlah kustomisasi halaman error pada folder /error untuk mengganti error kode pada Apache. Error kode yang perlu diganti adalah 404 Not Found dan 403 Forbidden.

---

bukalah AbimanyuWebServer dan ubah script [parikesit.abimanyu.it18.com](http://parikesit.abimanyu.it18.com/).conf dengan script berikut 

```bash
echo -e '<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/parikesit.abimanyu.it18
  ServerName parikesit.abimanyu.it18.com
  ServerAlias www.parikesit.abimanyu.it18.com

  <Directory /var/www/parikesit.abimanyu.it18/public>
          Options +Indexes
  </Directory>

  <Directory /var/www/parikesit.abimanyu.it18/secret>
          Options -Indexes
  </Directory>

  Alias "/public" "/var/www/parikesit.abimanyu.it18/public"
  Alias "/secret" "/var/www/parikesit.abimanyu.it18/secret"

  ErrorDocument 404 /error/404.html
  ErrorDocument 403 /error/403.html

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.it18.com.conf
```

lakukan restart apache dengan command berikut 

```bash
service apache2 restart
```

lakukan testing error pada client dengan command `lynx www.parikesit .abimanyu.it18.com/haloerror` sehingga didapatkan hasil seperti berikut 

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2019.png)

lakukan testing juga pada client dengan command `lynx www.parikesit .abimanyu.it18.com/secret` sehingga didapatkan hasil seperti berikut 

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2020.png)

---

### Soal 16

Buatlah suatu konfigurasi virtual host agar file asset **www.parikesit.abimanyu.yyy.com/public/js** menjadi **www.parikesit.abimanyu.yyy.com/js**

---

bukalah AbimanyuWebServer dan ubah config parikesit.abimanyu.it18.com.conf pada  /etc/apache2/sites-available/parikesit.abimanyu.it18.com dengan script berikut 

```bash
echo -e '<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/parikesit.abimanyu.it18
  ServerName parikesit.abimanyu.it18.com
  ServerAlias www.parikesit.abimanyu.it18.com

  <Directory /var/www/parikesit.abimanyu.it18/public>
          Options +Indexes
  </Directory>

  <Directory /var/www/parikesit.abimanyu.it18/secret>
          Options -Indexes
  </Directory>

  Alias "/public" "/var/www/parikesit.abimanyu.it18/public"
  Alias "/secret" "/var/www/parikesit.abimanyu.it18/secret"
  Alias "/js" "/var/www/parikesit.abimanyu.it18/public/js" //tambahan

  ErrorDocument 404 /error/404.html
  ErrorDocument 403 /error/403.html

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.it18.com.conf
```

lakukan restart apache dengan command berikut

```bash
service apache2 restart
```

lakukan testing pada client dengan command `lynx [www.parikesit.abimanyu.it18.com/js](http://www.parikesit.abimanyu.it18.com/js)` sehingga didapatkan hasil seperti berikut 

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2021.png)

---

### Soal 17

Agar aman, buatlah konfigurasi agar **www.rjp.baratayuda.abimanyu.yyy.com** hanya dapat diakses melalui port 14000 dan 14400.

---

bukalah AbimanyuWebServer dan ubah config pada /etc/apache2/sites-available/rjp.baratayuda.abimanyu.it18.com.conf dengan script berikut 

```bash
echo -e '<VirtualHost *:14000 *:14400>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/rjp.baratayuda.abimanyu.it18
  ServerName rjp.baratayuda.abimanyu.it18.com
  ServerAlias www.rjp.baratayuda.abimanyu.it18.com

  ErrorDocument 404 /error/404.html
  ErrorDocument 403 /error/403.html

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/rjp.baratayuda.abimanyu.it18.com.conf
```

lalu pada `/etc/apache2` tambahkan file `ports.conf`  dengan script seperti berikut 

```bash
echo -e '
# kalo pengen ngubah atau nambah port biasanya
# harus mengubah port pada conf ini
# /etc/apache2/sites-enabled/000-default.conf

Listen 80
Listen 14000
Listen 14400

<IfModule ssl_module>
        Listen 443
</IfModule>

<IfModule mod_gnutls.c>
        Listen 443
</IfModule>
' > /etc/apache2/ports.conf
```

Kemudian gunakan a2ensite untuk mengaktifkan (*ENABLE*) konfigurasi website yang telah dibuat seperti berikut 

```bash
a2ensite rjp.baratayuda.abimanyu.it18.com.conf
```

lakukan restart apache dengan command berikut 

```bash
service apache2 restart
```

masukkan resource data yang telah di berikan di soal dan kemudian lakukan testing di client dengan command `lynx [rjp.baratayuda.abimanyu.it18.com:14000](http://rjp.baratayuda.abimanyu.it18.com:14000/)` atau `[rjp.baratayuda.abimanyu.it18.com:14400](http://rjp.baratayuda.abimanyu.it18.com:14400)` sehingga di dapatkan hasil seperti berikut 

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2022.png)

testing juga menggunakan port yang berbeda, disini kami menggunakan port 144111 dengan command `lynx [rjp.baratayuda.abimanyu.it18.com:1441](http://rjp.baratayuda.abimanyu.it18.com:1441/)11` seperti berikut 

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2023.png)

---

### Soal 18

Untuk mengaksesnya buatlah autentikasi username berupa “Wayang” dan password “baratayudayyy” dengan yyy merupakan kode kelompok. Letakkan DocumentRoot pada /var/www/rjp.baratayuda.abimanyu.yyy.

---

bukalah AbimanyuWebServer dan 1. ubah config dalam /etc/apache2/sites-available/rjp.baratayuda.abimanyu.it18.com.conf dengan script berikut 

```bash
echo -e '<VirtualHost *:14000 *:14400>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/rjp.baratayuda.abimanyu.it18
  ServerName rjp.baratayuda.abimanyu.it18.com
  ServerAlias www.rjp.baratayuda.abimanyu.it18.com

  <Directory /var/www/rjp.baratayuda.abimanyu.it18>
          AuthType Basic
          AuthName "Restricted Content"
          AuthUserFile /etc/apache2/.htpasswd
          Require valid-user
  </Directory>

  ErrorDocument 404 /error/404.html
  ErrorDocument 403 /error/403.html

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/rjp.baratayuda.abimanyu.it18.com.conf
```

Kemudian gunakan a2ensite untuk mengaktifkan (*ENABLE*) konfigurasi website yang telah dibuat seperti berikut 

```bash
a2ensite rjp.baratayuda.abimanyu.it18.com.conf
```

lalu gunakan command dibawah ini untuk membuat atau memperbarui file berkas sandi (password file) yang digunakan dalam otentikasi HTTP dasar di server Apache 

```bash
htpasswd -c -b /etc/apache2/.htpasswd Wayang baratayudait18
```

lakukan restart apache dengan command dibawah ini

```bash
service apache2 restart
```

kemudian, lakukan testing pada client dengan command `lynx [rjp.baratayuda.abimanyu.it18.com:14000](http://rjp.baratayuda.abimanyu.it18.com:14000/)` sehingga didapatkan hasil seperti berikut 

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2024.png)

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2025.png)

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2026.png)

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2027.png)

---

### Soal 19

Buatlah agar setiap kali mengakses IP dari Abimanyu akan secara otomatis dialihkan ke **[www.abimanyu.yyy.com](http://www.abimanyu.yyy.com/) (alias)**

---

bukalah AbimanyuWebServer dan ubah file /etc/apache2/sites-available/000-default.conf dengan script berikut 

```bash
echo -e '<VirtualHost *:80>
    ServerAdmin webmaster@abimanyu.it18.com
    DocumentRoot /var/www/html

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    Redirect / http://www.abimanyu.it18.com/
</VirtualHost>' > /etc/apache2/sites-available/000-default.conf
```

kemudian, uji konfigurasi dari server web Apache (HTTP Server) untuk mengecek apakah konfigurasi tersebut valid atau mengandung kesalahan dengan command berikut 

```bash
apache2ctl configtest
```

lakukan restart apache dengan command berikut 

```bash
service apache2 restart
```

kemudian lakukan testing dengan command `lynx 192.242.1.4` sehingga didapatkan hasil seperti berikut 

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2028.png)

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2029.png)

---

### Soal 20

Karena website **www.parikesit.abimanyu.yyy.com** semakin banyak pengunjung dan banyak gambar gambar random, maka ubahlah request gambar yang memiliki substring “abimanyu” akan diarahkan menuju abimanyu.png.

---

bukalah AbimanyuWebServer dan aktifkan rewrite dengan command berikut 

```bash
a2enmod rewrite
```

lalu tambahkan script dibawah untuk .htaccess pada [parikesit.abimanyu.it18.com](http://parikesit.abimanyu.it18.com/)

```bash
echo 'RewriteEngine On
RewriteCond %{REQUEST_URI} ^/public/images/(.*)(abimanyu)(.*\.(png|jpg))
RewriteCond %{REQUEST_URI} !/public/images/abimanyu.png
RewriteRule abimanyu http://parikesit.abimanyu.it18.com/public/images/abimanyu.png$1 [L,R=301]' > /var/www/parikesit.abimanyu.it18/.htaccess
```

kemudian ubah juga script pada parikesit.abimanyu.it18.com.conf seperti berikut 

```bash
script
echo -e '<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/parikesit.abimanyu.it18

  ServerName parikesit.abimanyu.it18.com
  ServerAlias www.parikesit.abimanyu.it18.com

  <Directory /var/www/parikesit.abimanyu.it18/public>
          Options +Indexes
  </Directory>

  <Directory /var/www/parikesit.abimanyu.it18/secret>
          Options -Indexes
  </Directory>

  <Directory /var/www/parikesit.abimanyu.it18>
          Options +FollowSymLinks -Multiviews
          AllowOverride All
  </Directory>

  Alias "/public" "/var/www/parikesit.abimanyu.it18/public"
  Alias "/secret" "/var/www/parikesit.abimanyu.it18/secret"
  Alias "/js" "/var/www/parikesit.abimanyu.it18/public/js"

  ErrorDocument 404 /error/404.html
  ErrorDocument 403 /error/403.html

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.it18.com.conf
```

akukan restart apache dengan command berikut 

```bash
service apache2 restart
```

kemudian lakukan testing dengan command `lynx [parikesit.abimanyu.it18.com/public/images/abimanyu-student.jpg](http://parikesit.abimanyu.it18.com/public/images/abimanyu-student.jpg)` sehingga didapatkan hasil seperti berikut 

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2030.png)

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2031.png)

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2032.png)

lakukan juga testing menggunakan command `lynx [parikesit.abimanyu.it18.com/public/images/not-abimanyu.png](http://parikesit.abimanyu.it18.com/public/images/not-abimanyu.png)` seperti berikut 

![Untitled](lapres%2007c6b4e726964cb5b4879b624b1f0d63/Untitled%2033.png)

---
