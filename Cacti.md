# Monitoring with cacti
### Install packages yang diperlukan cacti
```bash
apt install snmp snmpd librrds-perl rrd-tool
```
### Install cacti dan cacti spine
```bash
apt install cacti cacti-spine
```
> pada bagian db-common pilih yes
> dan pada bagian mysql masukkan password mariadb
### config pada snmpd nya
```bash
nano /etc/snmp/snmpd.conf
```
> ubah pada bagian agentaddress dan dibawah rocommunity
```bash
agentaddress udp:192.168.2.2:161
rocommunity Nama_bebas 192.168.2.2
```
> Keluar dari file dan restart snmpdnya
```bash
systemctl restart snmpd
```
### buka cacti di chrome, ip/cacti
> create newa
