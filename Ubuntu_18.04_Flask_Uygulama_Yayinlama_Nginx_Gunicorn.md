## Gunicorn ve Nginx Kullanarak Ubuntu Vps Üzerine Flask Uygulama Yayınlama

#### Gerekli Bileşenlerin Kurulumu

Bu adımda Ubuntu vps'imizi güncelleyeceğiz ve üzerine gerekli bileşenleri kuracağız. 

```
sudo apt update

sudo apt install python3-pip python3-dev build-essential libssl-dev libffi-dev python3-setuptools
```

#### Python Sanal Ortamı Oluşturma

Bu adımda bir sanal ortam oluşturacağız. Böylece Flask uygulamamız izole ve bağımsız bir hal alacak. İlk olarak python3-venv kurulumu yapacağız.

```
sudo apt install python3-venv
```

Şimdi projemizi oluşturalım ve içinde bir sanal ortam oluşturalım.

```
mkdir ~/proje_adi

cd ~/proje_adi

python3 -m venv sanal_ortam_adi
```

Şimdi ise oluşturduğumuz sanal ortamı etkinleştireceğiz.

```
source sanal_ortam_adi/bin/activate
```

Eğer etkinleştirme başarılı bir şekilde sonlandıysa aşağıdaki gibi bir görüntü olacaktır.

```
(sanal_ortam_adi)user@host:~/proje_adi$
```

#### Flask Uygulaması Ayarları

Şimdi sanal ortamımızın içinde Flask ve Gunicorn kurulumlarını yapıp uygulamamızı oluşturmaya başlayabiliriz.

İlk olarak wheel paketini kuralım.

```
pip3 install wheel
```

Şimdi ise Flask ve Gunicorn kuralım.

```
pip3 install gunicorn flask
```

#### Örnek Bir Flask Uygulaması Oluşturma

Şimdi basit bir Flask projesi oluşturacağız. Bu örnek projeyi kendi projeniz ile değiştirebilirsiniz. 

**Not:** Benim tavsiyem ilk olarak bu dokümanı takip etmeniz daha sonra kendi projeniz ile ilgili ayarlamaları yapmanız.

Projemiz için bir dosya açalım.

```
(sanal_ortam_adi) $ nano ~/proje_adi/proje_adi.py
```

Açtığımız bu dosyanın içine aşağıdaki kodları yapıştıralım.

```Python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "<h1 style='color:blue'>Hello There!</h1>"

if __name__ == "__main__":
    app.run(host='0.0.0.0')
```

Şimdi dosyanızı kaydedip kapatın. 

Kaydetmek için: **Ctrl + O -> y**

Çıkış yapmak için: **Ctrl + x**

Şimdi bağlantı noktasına izin vermemiz gerekiyor.

```
(sanal_ortam_adi) $ sudo ufw allow 5000
```

Şimdi ise uygulamamızı çalıştıralım.

```
(sanal_ortam_adi) $ python3 proje_adi.py
```

Projeniz çalışmaya başlamış olmalı. Tarayıcıdan açıp bakmakta fayda var.

```
sunucunuzun_ip_adresi:5000
```

yazarak tarayıcınızda görüntüleyebilirsiniz. Şimdi **Ctrl + c** ile uygulamayı sonlandırın.

#### WSGI Giriş Noktası Oluşturma

Gunicorn sunucusunun uygulamamız ile etkileşime geçebilmesi için wsgi.py isimli bir dosya açalım.

```
(sanal_ortam_adi) $ nano ~/proje_adi/wsgi.py
```

Açılan dosyanın içine aşağıdaki kodları yapıştıralım.

```Python
from myproject import app

if __name__ == "__main__":
    app.run()
```

 Kaydedip çıkış yapalım.

#### Gunicorn Yapılandırma

Aşağıdaki komutları uygulayın.

```
(sanal_ortam_adi) $ cd ~/proje_adi

(sanal_ortam_adi) $ gunicorn --bind 0.0.0.0:5000 wsgi:app
```

Şimdi tekrar tarayıcınızı açın ve aşağıdaki gibi sunucunuzun ip adresini yazın.

```
sunucunuzun_ip_adresi:5000
```

yazarak tarayıcınızda görüntüleyebilirsiniz. Şimdi **Ctrl + c** ile uygulamayı sonlandırın.

Artık sanal ortamı etkinsizleştirip Gunicorn'u yapılandırmaya başlayabiliriz.

```
(sanal_ortam_adi) $ deactivate
```

Aşağıdaki komut ile uygulamanız için bir servis dosyası açmalısınız.

```
sudo nano /etc/systemd/system/proje_adi.service
```

Açılan dosyanın içini aşağıdaki gibi kendinize uygun bir şekilde doldurun.

```
[Unit]
Description=Gunicorn instance to serve proje_adi
After=network.target

[Service]
User=kullanici_adi
Group=www-data
WorkingDirectory=/home/kullanici_adi/proje_adi
Environment="PATH=/home/kullanici_adi/proje_adi/sanal_ortam_adi/bin"
ExecStart=/home/kullanici_adi/proje_adi/sanal_ortam_adi/bin/gunicorn --workers 3 --bind unix:proje_adi.sock -m 007 wsgi:app

[Install]
WantedBy=multi-user.target
```

Kaydedip çıkış yapalım ve ardından aşağıdaki komutları çalıştıralım.

```
sudo systemctl start proje_adi
sudo systemctl enable proje_adi
```

Uygulamanızın durumunu görmek için

```
sudo systemctl status proje_adi
```

yazın. 

#### Nginx'i Proxy İsteklerine Yapılandırma

Uygulamamızı yayına almadan önceki son adımımız arkadaşlar. Şimdi aşağıdaki komutu çalıştırın.

```
sudo nano /etc/nginx/sites-available/proje_adi
```

Ardından aşağıdakileri açılan dosyanın içine yapıştırın.

```
server {
    listen 80;
    server_name alan_adi www.alan_adi;

    location / {
        include proxy_params;
        proxy_pass http://unix:/home/kullanici_adi/proje_adi/proje_adi.sock;
    }
}
```

Şimdi kaydedip kapatın ve aşağıdaki komutları çağırın.

```
sudo ln -s /etc/nginx/sites-available/proje_adi /etc/nginx/sites-enabled

sudo nginx -t

sudo systemctl restart nginx

sudo ufw delete allow 5000

sudo ufw allow 'Nginx Full'
```

Artık tarayıcınızı açıp alan adınızı yazma vakti geldi. 

```
alan_adiniz
```
















