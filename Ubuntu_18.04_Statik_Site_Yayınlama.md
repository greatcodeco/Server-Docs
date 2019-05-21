## Ubuntu 18.04 VPS Üzerinde Statik Web Sitesi Yayınlama

#### Nginx Kurulumu

Eğer sisteminizde Nginx kurulu değil ise Nginx'i kurmanız gerekmektedir. Aşağıdaki komutu yazarak kurulum işlemini gerçekleştirebilirsiniz.

```
sudo apt install nginx
``` 

Nginx'i başlatma, durdurma, yeniden başlatma gibi işlemleri gerçekleştirmemizi sağlayan komutlar aşağıdaki gibidir.

```
sudo systemctl start nginx

sudo systemctl stop nginx

sudo systemctl restart nginx
```

Şimdi güvenlik duvarı için birkaç ayar yapalım. Bu ayarlar HTTP ve HTTPs trafiğine izin verecektir.

```
sudo ufw allow 'Nginx Full'

sudo ufw reload
```

Şimdi ise projenizi oluşturacağınız dizini yaratacağız.

```
cd ~

mkdir www

nano ~/www/index.html
```

Açılan dosyanın içine şimdilik bir şeyler yazalım.

Şimdi oluşturduğumuz **www** dizininin izinlerini değiştirelim. Böylece Nginx dosyalarına erişmesi sağlanacak.

```
chmod 0755 ~/www
```

Şimdi ise alan adımızı yönlendirme işlemini gerçekleştireceğiz.

```
sudo nano /etc/nginx/sites-available/site_adi
```

Açılan dosyanın içine aşağıdakileri yazın kaydedin ve çıkın.

```
server {
        listen 80;
        server_name alan_adiniz www.alan_adiniz;
        root /home/kullanici_adiniz/www;
        index index.html;
}
```

Şimdi ise sembolik bir bağlantı oluşturmalıyız. Bunun için ise: 

```
sudo ln -s /etc/nginx/sites-available/site_adi /etc/nginx/sites-enabled/
```

Şimdi son olarak Nginx servisini yeniden başlatmalıyız.

```
sudo systemctl restart nginx
```
