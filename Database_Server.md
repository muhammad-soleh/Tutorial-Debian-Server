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
wget https://files.phpmyadmin.net/phpMyAdmin/4.9.0.1/phpMyAdmin-4.9.0.1-all-languages.tar.gz
```

### 4. Selanjutnya kita extract file phpmyadmin nya 
```bash
tar zxvf phpMyAdmin-4.9.0.1-all-languages.tar.gz
```

### 5. selanjutnya kita pindahkan folder phpmyadmin nya ke /usr/share/phpMyAdmin
```bash
mv phpMyAdmin-4.9.0.1-all-languages /usr/share/phpMyAdmin
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
$cfg['blowfish_secret'] = 'STRINGOFTHIRTYTWORANDOMCHARACTERS'; /* YOU MUST FILL IN THIS FOR COOKIE AUTH!$

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
nano phpmyadmin.conf
```
>lalu paste saja dibawah ini ke dalam filenya
```bash
Alias /phpmyadmin /usr/share/phpMyAdmin
        <Directory /usr/share/phpMyAdmin/>
            AddDefaultCharset UTF-8
       <IfModule mod_authz_core.c>
         # Apache 2.4
         <RequireAny> 
          Require all granted
         </RequireAny>
       </IfModule>
       <IfModule !mod_authz_core.c>
         # Apache 2.2
         Order Deny,Allow
         Deny from All
         Allow from 127.0.0.1
         Allow from ::1
       </IfModule>
    </Directory>
    <Directory /usr/share/phpMyAdmin/setup/>
       <IfModule mod_authz_core.c>
         # Apache 2.4
         <RequireAny>
           Require all granted
         </RequireAny>
       </IfModule>
       <IfModule !mod_authz_core.c>
         # Apache 2.2
         Order Deny,Allow
         Deny from All
         Allow from 127.0.0.1
         Allow from ::1
       </IfModule>
    </Directory>
```
lalu di save ctrl x -> y
### 11. selanjutnya publikasi ke web server file dan membuat tmp
```bash
a2ensite phpmyadmin.conf
mkdir /ush/share/phpMyAdmin/tmp
chmod 777 /usr/share/phpMyAdmin/tmp
chown -R www-data:www-data /usr/share/phpMyAdmin
systemctl restart apache2
```

### 12. Selanjutnya kita akan membuat akses login ke phpmyadmin
```bash
mysql -u root -p 
CREATE USER 'tekaje22'@' IDENTIFIED BY 'tekaje22';
GRANT ALL PRIVILEGES ON *.* TO 'tekaje22'@'%' WITH GRANT OPTION;
QUIT
```

### 13.selanjutnya buka browser dan buka IP/phpmyadmin


