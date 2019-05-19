## Ubuntu Sunucu İçin Başlangıç Yapılandırması

#### Kullanıcı Oluşturma

İlk olarak bir **kullanıcı** oluşturacağız. Ardından **sudo** yetkisi vereceğiz.

```
adduser kullanici_adi

usermod -aG sudo kullanici_adi
```

(bknz. sudo)[https://wiki.ubuntu-tr.net/index.php?title=Sudo]

#### SSH Ayarları

Sunucunuza uzaktan bağlanacağınızı düşünecek olursak SSH kullanmanız çok yüksek bir ihtimal. Şimdi kişisel bilgisayarınızdan sunucuya uzaktan bağlanmanızı sağlayacak ayarları yapacağız.

(bknz. SSH)[https://www.hostinger.web.tr/rehberler/ssh-nedir]

Öncelikle kişisel bilgisayarınızda bir SSH anahtarı oluşturmalısınız. Bunun için aşağıdaki komutları kullanabilirsiniz.

```
cd ~

ssh-keygen
```

Tebrikler artık bir SSH anahtarınız var. Bu anahtar bilgisayarınızda **~/.ssh/id_rsa.pub** yolunda görebilirsiniz.

Şimdi sunucunuzda oluşturduğunuz kullanıcıya geçiş yapmalısınız. Bunun için aşağıdaki komutu çalıştırabilirsiniz.

```
su kullanici_adi
```

Artık kendi kullanıcınıza geçiş yaptınız. Artık .ssh dizini oluşturup ona izinler vermenin vakti geldi. Aşağıdaki komutları takip edebilirsiniz.

```
mkdir ~/.ssh

chmod 700 ~/.ssh
```

.ssh dizininde *authorized_keys* dosyasını oluşturup içini doldurma vakti geldi.

```
touch ~/.ssh/authorized_keys
```

Dosyamızı oluşturduk. Şimdi kişisel bilgisayarımızda **~/.ssh** yolundaki *id_rsa.pub* isimli dosyanın içindeki her şeyi kopyalayalım.

Ardından sunucuda oluşturduğumu *authorized_keys* isimli dosyayı açalım ve içine public anahtarımızı yapıştıralım.

```
nano ~/.ssh/authorized_keys

CTRL + Shift + V

CTRL + o ardından y

CTRL + x
```

Geriye yalnızca .ssh dizininin yazma iznini kısıtlamak kaldı. Bunun için ise sunucunuzda

```
chmod 600 ~/.ssh/authorized_keys
```

komutunu çalıştırmalısınız.
