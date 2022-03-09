# Setting Database Server (mariadb dan phpmyadmin)
### 1. Untuk menginstall phpmyadmin wajib menggunakan internet jadi mau memakai repo online atau dari dvd nya tetep wajib terkoneksi internet
> Install apache2
```bash
apt install apache2 apache2-utils -y
```
> Install php 
```bash
apt install -y php libapache2-mod-php php-cli php-fpm php-json php-pdo php-mysql php-zip php-gd  php-mbstring php-curl php-xml php-pear php-bcmath
```
> Install mariadb 
```bash
apt install -y mariadb-server mariadb-client
```

### 2. setelah semua terinstall, selanjutnya kita setting password rootnya
```bash
mysql_secure_installation
```
selanjutnya di enter sampai disuruh memasukkan new password 
```bash
New password: tekaje22
Re-enter new password: tekaje22
Password updated successfully!
Reloading privilege tables..
```
untuk selanjutnya pilih yes aja 

### 3. Setelah itu kita download phpmyadminnya menggunakan wget (disini saya membuat folder baru di folder root dengan nama download langkah ini tidak wajib)
```bash 
mkdir download
cd download
wget https://files.phpmyadmin.net/phpMyAdmin/5.1.3/phpMyAdmin-5.1.3-all-languages.tar.gz
```

### 4. Selanjutnya kita extract file phpmyadmin nya 
```bash
tar xvf phpMyAdmin-5.1.3-all-languages.tar.gz
```

### 5. selanjutnya kita pindahkan folder phpmyadmin nya ke /usr/share/phpMyAdmin
```bash
mv phpMyAdmin-5.1.3-all-languages/ /usr/share/phpMyAdmin
```

### 6. Langkah selanjutnya kita copy file config.sample.inc.php menjadi config.inc.php
```bash
cp -pr /usr/share/phpMyAdmin/config.sample.inc.php /usr/share/phpMyAdmin/config.inc.php
```

### 7. edit file config.inc.php 
```bash
nano /usr/share/phpMyAdmin/config.inc.php
/**
 * This is needed for cookie based authentication to encrypt password in
 * cookie. Needs to be 32 chars long.
 */
$cfg['blowfish_secret'] = 'STRINGOFTHIRTYTWORANDOMCHARACTER'; /* YOU MUST FILL IN THIS FOR COOKIE AUTH!$

/**
 * Servers configuration
 */
```
> lalu dibagian phpMyAdmin configuration storage settings. kita hapus semua tanda pagar dibagian Storage database and table dan menghapus 3 pagar di bagian user used manipulate with storage
```bash

/* User used to manipulate with storage */
 $cfg['Servers'][$i]['controlhost'] = 'localhost';
// $cfg['Servers'][$i]['controlport'] = '';
 $cfg['Servers'][$i]['controluser'] = 'pma';
 $cfg['Servers'][$i]['controlpass'] = 'pmapass';

/* Storage database and tables */
 $cfg['Servers'][$i]['pmadb'] = 'phpmyadmin';
 $cfg['Servers'][$i]['bookmarktable'] = 'pma__bookmark';
 $cfg['Servers'][$i]['relation'] = 'pma__relation';
 $cfg['Servers'][$i]['table_info'] = 'pma__table_info';
 $cfg['Servers'][$i]['table_coords'] = 'pma__table_coords';
 $cfg['Servers'][$i]['pdf_pages'] = 'pma__pdf_pages';
 $cfg['Servers'][$i]['column_info'] = 'pma__column_info';
 $cfg['Servers'][$i]['history'] = 'pma__history';
 $cfg['Servers'][$i]['table_uiprefs'] = 'pma__table_uiprefs';
 $cfg['Servers'][$i]['tracking'] = 'pma__tracking';
 $cfg['Servers'][$i]['userconfig'] = 'pma__userconfig';
 $cfg['Servers'][$i]['recent'] = 'pma__recent';
 $cfg['Servers'][$i]['favorite'] = 'pma__favorite';
 $cfg['Servers'][$i]['users'] = 'pma__users';
 $cfg['Servers'][$i]['usergroups'] = 'pma__usergroups';
 $cfg['Servers'][$i]['navigationhiding'] = 'pma__navigationhiding';
$cfg['Servers'][$i]['savedsearches'] = 'pma__savedsearches';
 $cfg['Servers'][$i]['central_columns'] = 'pma__central_columns';
 $cfg['Servers'][$i]['designer_settings'] = 'pma__designer_settings';
 $cfg['Servers'][$i]['export_templates'] = 'pma__export_templates';
```

### 8. jika sudah kita keluar ctrl + x -> y dan selanjutnya kita buat database nya dengan menggunakan command berikut
```bash
mysql < /usr/share/phpMyAdmin/sql/create_tables.sql -u root -p
```

### 9. sesudah itu kita beri akses control agar bisa digunakan phpmyadmin nya
```bash
mysql -u root -p
enter password : 'password yang tadi dibuat pas mysql_secure_installation'
```
> selanjutnya kita kasih akses pma@localhost ke seluruh phpmyadmin dengan command berikut
```bash
GRANT ALL PRIVILEGES ON phpmyadmin.* TO 'pma'@'localhost' IDENTIFIED BY 'pmapass';
```
```bash
FLUSH PRIVILEGES;
```
```bash
quit;
```
### 10. setelah itu kita publish phpmyadmin nya agar bisa di akses di browser
> buka folder /etc/apache2/sites-available/
```bash
cd /etc/apache2/sites-available/
nano 000-default.conf
```
>lalu paste saja dibawah ini dibawah DocumentRoot
```bash
Alias /phpmyadmin /usr/share/phpMyAdmin
```
lalu di save ctrl x -> y
### 11. selanjutnya publikasi ke web server file dan membuat tmp
```bash
mkdir /usr/share/phpMyAdmin/tmp
chmod 777 /usr/share/phpMyAdmin/tmp
chown -R www-data:www-data /usr/share/phpMyAdmin
systemctl restart apache2
```

### 12. Selanjutnya kita akan membuat akses login ke phpmyadmin
```bash
mysql -u root -p 
CREATE USER 'tekaje22'@'%' IDENTIFIED BY 'tekaje22';
GRANT ALL PRIVILEGES ON *.* TO 'tekaje22'@'%' WITH GRANT OPTION;
QUIT
```

### 13.selanjutnya buka browser dan buka IP/phpmyadmin

# Setting Wordpress 
### note (membuat database untuk wordpressnya)
```bash
mysql -u root -p
[mysql] > create database wordpress;
```
### 1. Kita Download dahulu file wordpress nya dari web nya wordpress
```bash
wget http://wordpress.org/latest
```

### 2. lalu kita extract file nya menggunakan command tar
```bash
tar xvf latest
```

### 3. Setelah itu kita pindahkan saja file wordpress nya ke /var/www/html
```bash
mv wordpress /var/www/html
```

### 4. lalu buka saja di chrome atau web browser lainnya 
```bash
http://ip_debian/wordpress
```
### jika ingin menambahkan wordpress ini ke dns yang sudah kalian buat maka tambahkan config berikut
> copy file /etc/apache2/sites-available/000-default.conf  menjadi ke file seterah kalian
```bash
cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/wordpress.conf
```
> Lalu kita buka file nya 
```bash 
nano /etc/apache2/sites-available/wordpress.conf
```
> Edit pada ServerName dan DocumentRootnya
```bash
ServerName dns_kalian
DocumentRoot /var/www/html/wordpress
```
> lalu kita enable site dulu
```bash
a2ensite wordpress.conf
```
> restart apache2
```bash
systemctl restart apache2
```

## Cara setting Wordpress nya 
### Setelah kalian buka di browser maka klik lets go aja
![gambar](https://github.com/muhammad-soleh/Tutorial-Debian-Server/blob/main/images/1.png)
### lalu masukkan username nya 'tekaje22' dan passwordnya 'tekaje22' lalu klik next aja ( untuk username dan password sesuai yang kita bikin diatas )
![gambar](https://github.com/muhammad-soleh/Tutorial-Debian-Server/blob/main/images/2.png)
### lalu copy semua yang di dalam kotak dan buka kembali debiannya
> buat file wp-config.php didalam folder wordpress
```bash
nano /var/www/html/wordpress/wp-config.php
```
> lalu paste aja semuanya
![gambar](https://github.com/muhammad-soleh/Tutorial-Debian-Server/blob/main/images/4.png)
> dan save filenya ctrl + x > y
### lalu balik ke wordpress dan klik run the installation
![gambar](https://github.com/muhammad-soleh/Tutorial-Debian-Server/blob/main/images/3.png)
### nah disini masukkan judul ke site title, masukkan username bebas, dan passwordnya juga bebas(jika password nya weak, ceklis aja confirm use password weak), dan email isi bebas. lalu klik go
![gambar](https://github.com/muhammad-soleh/Tutorial-Debian-Server/blob/main/images/5.png)
### lalu login aja menggunakan username dan password yang tadi dibuat
![gambar](https://github.com/muhammad-soleh/Tutorial-Debian-Server/blob/main/images/6.png)
### selesai


