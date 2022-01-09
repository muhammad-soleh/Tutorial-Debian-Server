# Setting repository online di debian server
### 1. Pastikan kalian sudah terkonek ke internet, testnya coba ping google.com atau ping 8.8.8.8
```bash
ping 8.8.8.8
```
> jika muncul reply berarti sudah terhubung

### 2. Selanjutnya buka file /etc/apt/sources.list
```bash
nano /etc/apt/sources.list
```
### 3. Setelah dibuka silahkan hapus semua isi filenya dan copy dibawah ini ke dalam file tersebut
```bash
deb http://deb.debian.org/debian buster main contrib non-free
deb-src http://deb.debian.org/debian buster main contrib non-free

deb http://deb.debian.org/debian buster-updates main contrib non-free
deb-src http://deb.debian.org/debian buster-updates main contrib non-free

deb http://deb.debian.org/debian buster-backports main contrib non-free
deb-src http://deb.debian.org/debian buster-backports main contrib non-free

deb http://security.debian.org/debian-security/ buster/updates main contrib non-free
deb-src http://security.debian.org/debian-security/ buster/updates main contrib non-free
```
>setelah itu save dan keluar tekan tombol ctrl +x > y >enter

### 4. setelah itu tinggal apt update
```bash
apt update
```
