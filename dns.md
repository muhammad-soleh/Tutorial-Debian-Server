# Konfigurasi DNS Server di Linux 

> DNS (Domain Name System) adalah salah satu sistem/service yang digunakan untuk mengtranslate/ mengubah alamat ip menjadi sebuah nama yang gampang diingat. Bayangkan jika kita harus mengingat ip address yang jumlahnya hingga jutaan, sehingga diadakan dns yang dimana kita hanya harus mengingat sebuah nama seperti google.com, facebook.com.

> Yang perlu diketahui dari DNS adalah forward dan reverse. Mudahnya seperti ini forward ialah yang memetakan domain ke alamat ip, sedangkan reverse digunakan untuk memetakan alamat IP ke dalam nama domain.

> Lalu kita juga perlu tau bahwa terdapat beberapa jenis DNS Record. Yang akan kita gunakan ialah 

| Jenis Record | Kegunaan                                                                                  |
|--------------|-------------------------------------------------------------------------------------------|
| NS Record    | Mendeskripsikan nama server untuk domain yang mengizinkan DNS lookup dengan several zones |
| MX Record    | Mengizinakn mail untuk dikirim ke tempat mail server yang benar dalam nama domain.        |
| A Record     | Digunakan untuk memetakan nama domain ke ip address                                       |

> Pada linux debian ,package yang digunakan ialah bind9. Selain itu kita juga memerlukan package dnsutils untuk melakukan pengujian dns server kita nanti. Oke kita lansung saja ke installasi nya.

### 1. apt install bind9 dnsutils
```bash
apt install bind9 dnsutils
```
![gambar](https://github.com/muhammad-soleh/Tutorial-Debian-Server/blob/main/images/dns_1.png)

### 2. Membuat forward zone 
> kita ikuti saja dulu seperti dibawah ini
```bash
cp named.conf.default-zones named.conf.local
nano named.conf.local
```
![gambar](https://github.com/muhammad-soleh/Tutorial-Debian-Server/blob/main/images/dns_2.png)

> lalu hapus saja semua nya hingga tersisa seperti dibawah ini
![gambar](https://github.com/muhammad-soleh/Tutorial-Debian-Server/blob/main/images/dns_3.png)
> lalu kita ganti localhost dengan domain kita misal disini kita akan menggunakan domain lehzo.co.id
![gambar](https://github.com/muhammad-soleh/Tutorial-Debian-Server/blob/main/images/dns_4.png)
> lalu bagian file nya juga diganti menjadi /etc/bind/db.lehzo (penamaan bebas asal ingat)
![gambar](https://github.com/muhammad-soleh/Tutorial-Debian-Server/blob/main/images/dns_5.png)
> lalu save saja terlebih dahulu ctrl-x , y


### 3. Lalu kita copy file db.local menjadi db.lehzo
> cp db.local db.lehzo
```bash
cp db.local db.lehzo
```

### 4. Lalu kita ubah isi nya sesuai dibawah ini
> nano db.lehzo
```bash
nano db.lehzo
```
> lalu kita ubah semua localhost menjadi nma domain  kita bisa menggunakan shortcut seperti dibawah ini
```bash 
ctrl + w , 
ctrl + r, 
lalu isi localhost enter, 
setelah itu isi nama domainnya enter lagi, 
lalu klik A
```
> maka semua localhost akan terubah ke domain yang kita ketik tadi. lalu sisanya ikuti yang diganti dibwah ini nanti akan saya jelaskan

```bash
@	IN	NS	lehzo.co.id 	; untuk mendefinisikan nama server domain kita

@	IN	A	192.168.119.24	; untuk mendifiniskan ip address utama dns lehzo.co.id

www	IN	A	192.168.119.24	; untuk mendifiniskan ip address  subdomain www.lehzo.co.id nantinya akan kita isi dns ini dengan wordpress

cacti	IN	A	192.168.119.24	; untuk mendifiniskan ip address  subdomain 
cacti.lehzo.co.id nantinya akan kita isi dns ini dengan monitoring server

mail 	IN	A	192.168.119.24	; untuk mendifiniskan ip address  subdomain mail.lehzo.co.id nantinya akan kita isi dns ini dengan webmail

@	IN	MX 10	mail		; untuk menandakan bahwa subdomain mail.lehzo.co.id nanti akan dibuat domain untuk mail server
```
> setelah semua diisi dengan benar lanjut save saja ctrl +x, y
> selesai sudah kita membuat forward zone nya selanjutnya kita akan settings reverse zone nya

### 5.  Langkah pembuatan reverse zone
> kita buka kembali file named.conf.local
```bash
nano named.conf.local
```
> lalu kita isikan seperti dibawah ini
> disini kita yang kita ubah bagian 127 menjadi 119.168.192, loh dapet darimana itu merupakan reverse dari ip address dns utama kita yaitu 192.168.119.24 nah nanti 24 nya kita isi difile db.192, 

> disini kita akan copy file db.127 ke file db.192
```bash
cp db.127 db.192
```

>lalu kita buka file nya
```bash
nano db.192
```

> sama seperti langkah sebelumnya kita ganti semua localhost menjadi nama domain kita hingga seperti dibawah ini

> lalu kita ubah lagi menjadi seperti ini
>oh iya disini ada jenis DNS record yaitu PTR disini tugas nya sama seperti A record tadi tapi disini mengubah alamat ip menjadi domain. dan kita disini tambahkan ip belakangnya tadi.

### 6. Setelah itu maka tinggal restart service bind9 nya
> systemctl restart bind9
```bash
systemctl restart bind9
```

### 7. lalu ada satu hal lagi yaitu menambahkan ip dns kita ke /etc/resolv.conf seperti dibawah ini
### 8. setelah itu kita coba menggunakan tools nslookup 
> (jika command not found berarti dnsutils nya belum diinstall)
```bash
nslookup lehzo.co.id
nslookup 192.168.119.24
```
### 9. jika muncul seperti dibawah maka sudah berhasil selanjutnya kita akan coba di windows.
> Pertama kita klik Windows + R.
> lalu kita isi ncpa.cpl enter
> lalu kita klik di interface yang terhubung ke linux kita ,lalu klik properties
> lalu klik internet protocol v4
> lalu ubah dibagian bawah menjadi 192.168.119.24
> lalu klik ok dan buka command prompt
> lalu kita ping domainnya

### 10. Maka selesai sudah konfigurasi dns server kita jika ada yang ingin ditanyakan silahkan langsung ke linkedin saya www.linkedin.com/in/muhammad-soleh











