### Assignment ###

**System requirement:** 

- OS             : `Ubuntu Server 20.04` , `Virtualbox`

- Software       : `Postfix`, `Dovecot`, `Postgresql` , `Apache2`

**STEP 1 : INSTALLASI dan KONFIGURASI DNS**

- `apt install bind9`

- `ufw allow 53`

- `nano /etc/netplan/01-network-manager-all.yaml` // setelah ip sudah di ganti , jalankan command `net apply`

- `nano /etc/resolv.conf` // ganti ip name server `192.168.22.207` simpan dengan `ctrl+x` 

- `nano /etc/hosts` // tambahkan di baris ketiga `192.168.22.207 domainsaya.com` simpan dengan `ctrl+x`

- `nano /etc/bind/named.conf.local`

- `nano /etc/bind/db.domainsaya`

- `nano /etc/bind/db.192`

- `nano /etc/bind/named.conf.options`

- `systemctl restart bind9.service`

- `nslookup domainsaya.com`

- `nslookup 192.168.22.207`

**STEP 2 : INSTALLASI dan KONFIGURASI POSTFIX**

- `apt-get update`

- `apt-get install postfix`

- muncul `postfix configuration` klik `ok` general type `internet site` system mail name `domainsaya.com` lalu `ok`

- `dpkg-reconfigure postfix` klik `ok` kecuali untuk force synchronous pilih `no`

- `nano /etc/postfix/main.cf` //setting di baris akhir `home_mailbox = Maildir/`

- `systemctl restart postfix`

**STEP 3 : INSTALLASI dan KONFIGURASI DOVECOT**

- `apt-get install dovecot-imapd dovecot-pop3d`

- `cd /etc/dovecot/conf.d`

- `10-maail.conf`

- `nano 10-mail.conf` // ubah konfigurasi menjadi `mail_location = maildir :~/Maildir`

- `systemctl restart dovecot`

**STEP 4 : MEMBUAT USER,MENGIRIM dan MENERIMA EMAIL**

**Membuat user**

- `adduser bagas` // User satu 

- `password ..`

lalu tekan `yes`

- ` adduser d-net `

- ` password .. ` //user dua

- Cek user yang sudah dibuat ` nano /etc/group `

**Mengirim E-mail**

- ` telnet domainsaya.com smtp `

- ` mail from: bagas `

- ` rcpt to: d-net `

- ` data ` // untuk mengisi pesan E-mail

- ` quit `

**Menerima Email**

- ` telnet domainsaya.com pop3 `

- ` user d-net `

- ` pass .. `

- ` stat ` // untuk melihat apakah sudah ada pesan masuk

- ` retr 1 ` // melihat isi pesan 


**STEP 5: INSTALL DAN KONFIGURASI WEBMAIL MENGGUNAKAN SQUIRRELMAIL**

- ` apt-get install apache2 `

- ` cd /home/tika/Downloads `

-  wget  ` https://sourceforge.net/projects/squirrelmail/files/stable/1.4.22/squireelmail-webmail-1.4.22.zip `

- ` unzip squireelmail-webmail-1.4.22.zip  `

-  ` squireelmail-webmail-1.4.22 /var/www/html/mail `

- ` cd /var/www/html/mail ` lalu ` ls ` untuk melihat file 

- ` chown -R www-data:www-data/var/www/html/mail ` 

- ` chmod 755 -R /var/www/html/mail ` // untuk mengatur hak akses 

- ` perl /var/www/html/mail/config/conf.pl `

- jika sudah masuk dalam file ketik command ` 1 ` ubah oganization name menjadi ` domainsaya `

- lalu return ketik ` r ` dan masuk ke angka ` 1 ` ubah domain name menjadi ` domainsaya.com `

- Data Directory = ` /var/www/html/mail/data `

- Attachment Directory = ` /var/www/html/mail/data `

- Allow server-side sorting = ` true ` 

- ` Systemctl restart apache2 `

- Buka Firefox pada Ubuntu , lalu ketikan domain yang telah dibuat ` domainsaya.com/mail `

- Login pada ` SquirrelMail ` menggunakan ` username ` dan ` password ` yang telah dibuat 

- Coba untuk mengirim dan menerima email 


**STEP 6: INSTALL DAN KONFIGURASI SECURITY MAIL SERVER**

- ` apt-get install gnupg2 ` 

- ` gpg --gen-key `

- Konfigurasi untuk use ID , ` Real name: Tika  Email Address: Tikaastriani17@gmail.com ` lalu tekan ` o ` 

- Lalu akan muncul tampilan ` Protect your new key ` isikan sesuai dengan keinginan lalu ` OK ` 

- ` gpg --export -a tika > domainsaya.key `

- ` cd /tika/Downloads `

- ` nano mail2.txt ` isikan pesannya lalu ` ctrl+x ` 

- Untuk mengenkripsi pesan ` gpg --encrypt -r tika -a mail2.txt ` lalu ` ls ` untuk mengecek apakah file sudah terbuat 

- untuk melihat pesan yang sudah terenskripsi ` nano mail.txt.asc ` 

- ` mkdir attach '

- Lakukan ` chown -R www-data:www-data/var/www/html/mail/attach  ` dan ` chmod 755 -R /var/www/html/mail/attach ` 

- ` Systemctl restart apache2 `

- Login kembali pada ` SquireeMail ` mencoba untuk mengirimkan pesan dengan ` attach ` file yang sudah di enskripsi dari ` user 1 ` ke ` user 2 `

- Untuk melihat apakah pesan tersebut berhasil dikirimkan , login ke ` user 2 ` dan download ` file encrypt ` 

- Untuk mendekripsi file tersebut masuk ke direktori ` Downloads ` lakukan ` gpg --decrypt mail.txt.asc `

- Masukkan ` Passpharase ` lalu ` OK ` maka akan terlihat isi pesannya

**STEP 7: INSTALL ANTI SPAM MENGGUNAKAN SPAMASAASSIN**

- ` apt-get install spamassassin spamc ` 

- ` nano /etc/mail/spamssassin/local.cf ` hapus ` tanda # ` pada ` rewrite , report_safe ` lalu ` ctrl+x `  

- Update spamassassin ` sa-update ` 

- ` useradd spamd -s /bin/false -d /usr/bin/spamssassin ' dan cek pada ` nano /etc/group ` 

- ` nano /etc/postfix/master.cf ` 

- ` systemctl restart postfix ` 

- Pastikan status sudah aktif ` systemctl status postfix ` 

- ` systemctl restart spamassasin ` 

- Masuk pada web ` spamassassin.apache.prg/gtube ` ambil kode pada ` The GTUBE ` untuk mengecek apakah spam sudah berjalan 

- Login ` SquirreMail ` dan coba untuk mengirim ` Kode ` tersebut 

- Lalu cek ke penerima apakah sudah berhasil 

***Konfigurasi Spam Secara Otomatis***
- ` nano /etc/mail/spamassassin/local.cf ` untuk mengkonfigurasi  ` header ataupun kata-kata yng mengandung spam `

- ` systemctl restart spamassassin `  ` systemctl restart apache2 ` ` systemctl restart postfix `

- Selanjutnya login kembali pada ` SquireelMail ` lalu coba untuk mengirimkan pesan dengan kata yang mengandung ` spam ` 
 
