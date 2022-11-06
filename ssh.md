#Konfigurasi ssh di Linux.

> SSH (secure shell) adalah salah satu protokol yang digunakan untuk remote. Port SSH secara default adalah 22, namun bisa kita ganti untuk keamanan. Di tutorial kali ini saya akan mengkonfigurasi ssh dengan konfigurasi port 51234, dan Root diizinkan untuk remote(ingat konfigurasi ini sangat tidak disarankan namun untuk memudahkan konfigurasi selanjutnya dan saya izinkan root untuk login). Langsung saja ke konfigurasi nya, pastikan os nya sudah mendapatkan internet dan update repository.

### 1. Kita install ssh nya terlebih dahulu
```bash
apt install ssh -y
```

### 2. selanjutnya buka file konfig sshnya
```bash
nano /etc/ssh/sshd_config
```

### 3. di sini ada 2 yang diubah bagian port dan permitRootLogin
```bash
port 51234
permitRootLogin yes
```

### 4. setelah itu kita restart service sshnya
```bash
systemctl restart ssh
```

### 5. Setelah itu kita bisa setting di clientnya menggunkan putty dll.

