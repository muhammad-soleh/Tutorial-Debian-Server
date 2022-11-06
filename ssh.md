# Konfigurasi SSH di Linux.

> SSH (secure shell) adalah salah satu protokol yang digunakan untuk remote. Port SSH secara default adalah 22, namun bisa kita ganti untuk keamanan. Di tutorial kali ini saya akan mengkonfigurasi ssh dengan konfigurasi port 51234, dan Root diizinkan untuk remote(ingat konfigurasi ini sangat tidak disarankan namun untuk memudahkan konfigurasi selanjutnya dan saya izinkan root untuk login). Langsung saja ke konfigurasi nya, pastikan os nya sudah mendapatkan internet dan update repository.

### 1. Kita install ssh nya terlebih dahulu
```bash
apt install ssh -y
```
![gambar](https://github.com/muhammad-soleh/Tutorial-Debian-Server/blob/main/images/ssh_1.png)

### 2. Selanjutnya buka file konfig sshnya
```bash
nano /etc/ssh/sshd_config
```
![gambar](https://github.com/muhammad-soleh/Tutorial-Debian-Server/blob/main/images/ssh_2.png)

### 3. Di sini ada 2 yang diubah bagian port dan permitRootLogin
```bash
port 51234
permitRootLogin yes
```
![gambar](https://github.com/muhammad-soleh/Tutorial-Debian-Server/blob/main/images/ssh_3.png)

### 4. Setelah itu kita restart service sshnya
```bash
systemctl restart ssh
```
![gambar](https://github.com/muhammad-soleh/Tutorial-Debian-Server/blob/main/images/ssh_4.png)

### 5. Setelah itu kita bisa setting di clientnya menggunkan putty dll.
> disini hostname diisi dengan ip address linux dan port diisi dengan port yang digunakan contoh 51234
![gambar](https://github.com/muhammad-soleh/Tutorial-Debian-Server/blob/main/images/ssh_5.png)

