# implementing-dns-server
ابتدا ابزار 
bind9  
  رو نصب میکنیم

```
apt install bind9
```
سپس برای
DNS server
 آیپی
استاتیک ست میکنیم
```
nano /etc/network/interfaces
```
>allow-hotplug enp0s3    
iface enp0s3 inet dhcp

to 

>auto enp0s3       
iface enp0s3 inet static      
address 192.168.10.1  
netmask 255.255.255.252   
dns-nameserver 127.0.0.1   

سپس در فایل 
/etc/bind/named.conf.options
تغییرات زیر رو اعمال میکنیم که در صورت نبود ریکورد مورد نظر به ایپی دیگه ای مراجعه بشه 
‍‍
```
nano /etc/bind/named.conf.options
```   
>// forwards {    
//    0.0.0.0;   
// };  
 
 to   
 >forward {
 8.8.8.8
 };

بعد از ان 
‍‍
```
nano /etc/bind/named.conf.local
```
>zone "srv01"{   
    type master;   
    file "/etc/bind/db.srv01";   
};   
zone "srv02"{   
    type master;   
    file "/etc/bind/db.srv02;   
};   
سپس فایل 
db.empty 
رو به عنوان تپملیت با نام 
db.srv01
 و
 db.srv02 
 کپی میکنیم 
 ```
 cp /etc/bind/db.empty /etc/bind/db.srv01
 cp /etc/bind/db.empty /etc/bind/db.srv02
 ```
سپس تغییرات زیر و اعمال میکنیم
```
nano /etc/bind/db.srv01
```
>@  IN  SOA localhost.  root.localhost.(    

to

>@  IN  SOA srv01.  root.localhost.(

و   

>   IN  A 192.168.10.1
```
nano /etc/bind/db.srv02
```
>@  IN  SOA localhost.  root.localhost.(   

to

>@  IN  SOA srv02.  root.localhost.(

و

> IN  A 192.168.10.2  

سپس 
سرویس 
dns 
رو ریلود میکنیم 
```
systemctl reload bind9
```
پس از ان با کامند زیر میتوان لاگ سرویس هارو مشاهده کرد 
```
journalctl -xe
```    


![image](https://user-images.githubusercontent.com/88885103/189644275-d457a306-119c-4d15-a57a-5aa2373bea87.png)      

در فایل زیر 
nameserver 
رو به آیپی 
dns server 
تغییر میدهیم

```
nano /etc/resolv.conf
```
>nameserver 8.8.8.8   

to   

>nameserver 192.168.10.1   

و برای احتیاط
سرور رو ریبوت میکنیم 
بعد از بوت شدن سرور میتوان با کامند های 
```
host srv01
```
یا
```
host srv02
```
از درستی کارکرد
dns server 
اطمینان حاصل کرد 

![image](https://user-images.githubusercontent.com/88885103/189646791-d9a27e31-c47e-411d-b04c-3957199e8b2f.png)



