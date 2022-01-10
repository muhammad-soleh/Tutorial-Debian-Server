# Setting wordpress di debian server
### 1. Pastikan database sudah di setting caranya bisa dilihat di github sebelumnya
### 2. Pertama-tama kita buat dulu database untuk wordpress
>login ke database
```bash
mysql -u root -p 
Enter password : 'masukkan password kalian'
```
> kita buat database baru dengan nama db_wordpress
```bash
CREATE DATABASE db_wordpress;
```
> kita kasih user database dan kasih permissions (disini saya pakai user tekaje dengan password tekaje)
```bash
GRANT ALL ON db_wordpress.* TO 'tekaje'@'localhost' IDENTIFIED BY 'tekaje';
```
> refresh privileges
```bash
FLUSH PRIVILEGES;
```
> keluar dari mysql
```bash
EXIT;
```

### 2. Selanjutnya kita download file wordpressnya
> kita buka folder /var/www/html
```bash
cd /var/www/html
```
> kita download file wordpressnya pakai curl atau wget
```bash
curl -O https://wordpress.org/latest.tar.gz
```
> selanjutnya kita extract
```bash
tar xvf latest.tar.gz
```
> kita hapus aja file latest.tar.gznya
```bash
rm latest.tar.gz
```

### 3. Setelah itu kita konfigurasi wordpressnya
> kita ubah dulu owner dari folder wordpressnya
```base
chown -R www-data:www-data /var/www/html/wordpress
```
> kita ubah permissions untuk file menjadi 640 sedangkan folder 750
```bash
find /var/www/html/wordpress/ -type d -exec chmod 750 {} \;
find /var/www/html/wordpress/ -type f -exec chmod 640 {} \;
```
> kita masuk ke folder wordpress dan ganti file wp-config-sample.php
```bash
cd /var/www/html/wordpress/
mv wp-config-sample.php wp-config.php
```
> edit file wp-config.php
```bash
nano wp-config.php
```
> scroll kebawah dan temukan mysql settings ganti dibagian db_name(nama database) db_user(user database) db_password(password database)
```bash
// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define( 'DB_NAME', 'database_name_here' );
/** MySQL database username */
define( 'DB_USER', 'username_here' );
/** MySQL database password */
define( 'DB_PASSWORD', 'password_here' );
/** MySQL hostname */
define( 'DB_HOST', 'localhost' );
``` 

### 4. Selanjutnya kita secure wordpress kita biar aman
> kita generate secret key baru
```bash
curl -s https://api.wordpress.org/secret-key/1.1/salt/
```
> nanti akan ada format seperti dibawah ini tapi isinya beda
```bash
define('AUTH_KEY',         '');
define('SECURE_AUTH_KEY',  '');
define('NONCE_KEY', ''); 
define('AUTH_SALT', '');
define('LOGGED_IN_SALT',   '');
define('NONCE_SALT',       '');
```
> copy aja semua nya dan buka lagi file wp-config.php dan cari yang formatnya sama dan hapus semuanya serta paste 

### 5. Selanjutnya kita configure apache untuk wordpressnya 
> buka folder /etc/apache2/sites-available
```bash
cd /etc/apache2/sites-available
```
ada 2 cara jika kita ingin mengakses lewat ip/wordpress tinggal buka saja file 000-default.conf tapii jika ingin menggunakan dns maka cp file 000-default.conf ke file wordpress.conf.
> ini jika kita mau akses lewat ip/wordpress
```bash
nano 000-default.conf
```
> dibawah DocumentRoot tambahkan dibawah ini
```bash
DocumentRoot /var/www/html/wordpress

<Directory /var/www/html/wordpress/>
AllowOverride All
</Directory>
```
> keluar dari file tadi
```bash
a2enmod rewrite
apache2ctl configtest
systemctl restart apache2
```
> lalu jika ingin menggunakan dns kita copy file 000-default.conf ke file baru dengan nama bebas disini namanya wordpress
```bash
cp 000-default.conf wordpress.conf
```
> kita buka file wordpress.conf dan edit dibagian Server name dengan nama dns nya, document root juga diganti, dan tambahkan juga yang sama seperti diatas.
```bash
ServerName www.mamang.net

ServerAdmin webmaster@localhost
DocumentRoot /var/www/html/wordpress
<Directory /var/www/html/wordpress/>
AllowOverride All
</Directory>
```
> setelah itu kita a2ensite file wordpress
```bash
a2ensite wordpress.conf
```
> lalu restart apache2
```bash
systemctl restart apache2
```

