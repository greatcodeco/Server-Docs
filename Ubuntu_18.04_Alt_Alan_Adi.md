## Sunucumuza Alt Alan Adı Eklemek

Merhaba, bu dokümanda çok uğraştığım ama aslında oldukça basit olan alt alan adını (subdomain) nasıl ekleyeceğimizi anlatacağız. İlk olarak alan adını aldığınız yerden dns yönetim kısmına girip aşağıdaki görselde işaretli olan alanı eklemeniz gerekmektedir.

<a href="url"><img src="https://user-images.githubusercontent.com/20879375/57987597-4d282f00-7a8c-11e9-8a5c-307111b781aa.png"></a>

Bundan sonrası ise [Flask yayına alma](https://github.com/greatcodeco/Server-Docs/blob/master/Ubuntu_18.04_Flask_Uygulama_Yayinlama_Nginx_Gunicorn.md) dokümanında belirttiğimiz gibi ilerlemeli. Kısaca anlatmak gerekirse,

İlk olarak yeni bir kullanıcı açıyoruz. [Bu dokümanı](https://github.com/greatcodeco/Server-Docs/blob/master/Ubuntu_18.04_Baslangic_Ayarlari.md) aynen uyguladıktan sonra Flask yayına alma dokümanını uygulayın. Yalnızca dokümandaki

```
sudo nano /etc/nginx/sites-available/proje_adi
```
bölümünde açılan dosyanın içeriğini 

```
server {
    listen 80;
    server_name deneme.pyerzurum.me www.deneme.pyerzurum.me;

    location / {
        include proxy_params;
        proxy_pass http://unix:/home/alt/deneme/deneme.sock;
    }
}
```

olarak değiştirin. Yani alt alan adınız ismini girmeniz yeterlidir.

Sayfanızı yenilediğinizde eğer hala alt alan adınız görünmüyor ise biraz beklemenizi tavsiye ederiz.
