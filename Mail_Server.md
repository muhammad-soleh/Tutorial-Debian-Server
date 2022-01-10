# Settings Mail Server di debian 10
### 1. Untuk membuat mail server dibutuhkan domain dulu
> pertama-tama kita install dulu bind9 dnsutils bind9utils
```bash
apt install bind9 dnsutils bind9utils -y
```
> disini kita akan buat domain coba.net dan subdomain mail.coba.net.
> pertama konfigurasi file /etc/bind/named.conf.local
```bash
zone "coba.net" {
        type master;
        file "/etc/bind/coba.db";
};

zone "99.16.172.in-addr.arpa" {
        type master;
        file "/etc/bind/172.db";
};
```
> selanjutnya kita setting file /etc/bind/coba.db
```bash
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     coba.net. root.coba.net. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      coba.net.
@       IN      A       172.16.99.1
mail    IN      A       172.16.99.1
coba.net.       IN      MX 10   mail.coba.net.
@       IN      AAAA    ::1

```
> selanjutnya file /etc/bind/172.db
```bash
;
; BIND reverse data file for local loopback interface
;
$TTL    604800
@       IN      SOA     coba.net. root.coba.net. (
                              1         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      coba.net.
1       IN      PTR     coba.net.
1       IN      PTR     mail.coba.net.
```
> Jika Sudah coba kita lakukan restart pada service bind dan juga edit nameserver pada /etc/resolv.conf
```bash
systemctl restart bind9
nano /etc/resolv.conf
```
> edit menjadi ip nya
```bash
nameserver 172.16.99.1
```
> Jika sudah maka coba jalankan command nslookup pada domain coba.net dan mail.coba.net jika berhasil maka kita bisa lanjut ke langkah berikutnya

### 2. Selanjutnya kita install lamp nya sama seperti di database server jadi bisa dibaca di bagian database_server.md

### 3. Menginstall dan konfigurasi postfix(MTA) dan Dovecot (MDA)
> Install dulu postfix dan dovecotnya
```bash
apt install postfix dovecot-imapd dovecot-pop3d
```
> jika muncul package configuration pilih seperti dibawah ini
```bash
jika muncul general type of mail pilih internet site 
jika muncul system mail name itu dimasukkan domainnya (coba.net)
```
> selanjutnya kita buat mail directory untuk menampung record email dari user
```bash
maildirmake.dovecot /etc/skel/Maildir
```
> Jika sudah maka kita menambahkan konfigurasi berikut pada bagian paling bawah untuk mengaktifkan Fungsi Mail Direktori pada file /etc/postfix/main.cf
```bash
home_mailbox = Maildir/
```
> Lalu lakukan konfigurasi seperti dibawah ini juga pada  /etc/dovecot/conf.d/10-mail.conf. cukup hapus tanda pagar pada bgian mail_location = maildir:~/Maildir dan berikan tanda pagar pada bagian mail_location = mbox:~/mail:INBOX=/var/mail/%u belum ada tanda pagar.
```bash
nano /etc/dovecot/conf.d/10-mail.conf

mail_location = maildir:~/Maildir

#   mail_location = mbox:~/mail:INBOX=/var/mail/%u
#   mail_location = mbox:/var/mail/%d/%1n/%n:INDEX=/var/indexes/%d/%1n/%n
#
# <doc/wiki/MailLocation.txt>
#
#mail_location = mbox:~/mail:INBOX=/var/mail/%u
```
> konfigurasi pada file /etc/dovecot/conf.d/10-auth.conf
```bash
nano /etc/dovecot/conf.d/10-auth.conf
##
## Authentication processes
##

# Disable LOGIN command and all other plaintext authentications unless
# SSL/TLS is used (LOGINDISABLED capability). Note that if the remote IP
# matches the local IP (ie. you're connecting from the same computer), the
# connection is considered secure and plaintext authentication is allowed.
# See also ssl=required setting.
disable_plaintext_auth = no
```
> jika sudah maka kita reconfigure postfix 
```bash
dpkg-reconfigure postfix
jika muncul notip sama kaya sebelumnya maka pilih internet site
system mail juga diisi domain (coba.net)
lalu ok saja sampai di Local networks: disini kita tambahkan network dari ip nya misal 172.16.99.0 lalu enter terus sampai ketemu dengan internet protocols pilih 1pv4 enter dan selesai
```
> selanjutnya restart postfix dan dovecot
```bash
systemctl restart postfix dovecot
```

### 4. Instalasi dan konfigurasi Roundcube
> pertama install roundcube
```bash
apt install roundcube
```
> pilih yes jika muncul configure databse with dbconfig-common
> masukkan password untuk roundcube nya di bagian mysql application password for roundcube
> masukkan password yang sama dengan sebelumnya
> sesudah itu kita settings file roundcubenya di file /etc/roundcube/config.inc.php
```bash 
nano /etc/roundcube/config.inc.php

$config['default_host'] =coba.net';
$config['smtp_server'] =coba.net';
$config['smtp_user'] = '';
$config['smtp_pass'] = '';
```
> jika sudah disave dan lanjut membuat virtual host untuk roundcubenya
```bash
cd /etc/apache2/sites-available
cp 000-default.conf roundcube.conf
nano roundcube.conf

ServerName mail.coba.net
ServerAdmin webmaster@localhost
DocumentRoot /var/lib/roundcube
```
>selanjutnya kita aktifkan virtual hostnya
```bash
a2ensite roundcube.conf
systemctl restart apache2
```
> jika sudah kita coba buat dua user 
```bash
adduser joni
adduser jeany
```
> lalu cek apakah sudah ada folder Maildir di home directory masing-masing
```bash
ls /home/joni
ls /home/jeany
```
### 5. Cara mengetest apakah berhasil atau tidak bisa menggunakan telnet
```bash
telnet mail.coba.net 25
mail from: joni
rcpt to: jeany
data
hehe halo jeany
.
quit
```
```bash
telnet mail.coba.net 110
user jeany
pass 1 #masukkan password usernya
stat
retr 1
# jika disini ada isinya berarti sudah berhasil
quit
```
untuk test menggunakan roundcube dan thunder bird nanti menyusul





