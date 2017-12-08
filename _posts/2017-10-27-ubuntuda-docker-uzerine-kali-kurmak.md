---
layout: post
title:  "Ubuntu'da Docker üzerinde Kali Linux çalıştırmak"
description: "Her zaman Kali'ye ihtiyaç olmuyor ama gerektiğinde de kolayca bulmak zorlaşıyor. Kimileri RaspPI üzerinde Kali çalıştırırken kimileri VM üzerinde çalıştırmayı tercih edebiliyor. Ben Docker tercih ettim. Şimdilik fena gözükmüyor."
date:   2017-10-27 20:08:15
categories: security
tags: ['security', 'kali', 'linux', 'docker']
image: /assets/article_images/2017-10-27-ubuntuda-docker-uzerine-kali-kurmak/goddess-kali.jpg
published: true
---
# Taşınabilir Kali
Kali Linux security ve pentest konularında sanırım en çok tercih edilen dağıtımlardan birisi. Her şey elinizin altında ve gayet güzel çalışıyor. [Offensive Security](https://www.offensive-security.com/) grubuna bir teşekkür de ben etmek istiyorum.

Tabi pentest oturduğunuz yerden yapılmıyor. Bir çok zaman farklı networklere alarak, farklı interfacelere bağlanarak çalışmak gerekli. Bir bootable/live USB + taşınabilir bilgisayarla kullanmak en sık karşılaşılan yöntemdir sanıyorum ki. Ama kimileri de Kali'yi Raspberry Pi üzerine kurup oradan çalıştırıyor. Her yiğidin bir yoğurt yiyişi var diyelim ve kendi yiğitliğimizi Docker konteynırlarında saklayalım.

# Ubuntu'ya Docker yüklemek

Gayet basit bir işlemle Docker yükleyebiliyoruz. Docker'cı abiler ablalar bizim için bir script hazırlamışlar. [Get Docker](https://get.docker.com/) scriptini indirip çalıştırarak Docker'ı sistemimize kurabiliyoruz.

`$ curl -fsSL get.docker.com | bash`

Eğer sisteminizde curl yoksa `$ sudo apt-get install curl` komutuyla yüklemenizi tavsiye ederim. Tabi curl elzem değil ama zaten Docker yüklenirken curl'ü de yükleyecek, ha şimdi yüklemişsiniz ha sonra.

Gelin bir de Docker'ın çalıştığından emin olalım:

`$ sudo docker run hello-world`

# Kali Linux kurulumu

Bu kısım da bir önceki kadar basit. Docker pull ile Kali imajını çekeceğiz. Sonra da imajı çalıştırıp Kali'nin shell'ine geçeceğiz. Detayları [buradan](https://www.kali.org/news/official-kali-linux-docker-images/) öğrenebilirsiniz.

```
$ sudo docker pull kalilinux/kali-linux-docker
$ sudo docker run -t -i kalilinux/kali-linux-docker /bin/bash
```

Herhangi bir hata yoksa Docker üzerindeki Kali'ye root olarak bağlanmış olmalısınız. Gelin update komutunu çalıştıralım:

`root@0129d62d2319:/# apt-get update`

Kali'nin bu dağıtımında çalıştırılabilir pentest uygulamaları yok denecek kadar az (belki de yok). Nmap bile yok, ben öyle söyleyeyim. İsterseniz nmap yükleyebilirsiniz:

`# apt-get install nmap`

Hatta Kali Top 10 uygulamalarını yükleyerek nmap dahil bir çok uygulamayı indirebilirsiniz. Sanırım 2.5 - 3 GB'lık bir download sözkonusu, haberiniz olsun.

`# apt-get install kali-linux-top10`

Ancak yükleyeceğiniz tüm programlar siz shell'den çıkınca Docker kapanacağı için kaybolacaktır. Bunun önüne geçmek için container'ı image olarak kaydedebiliriz.

# Docker image'ını kaydetmek

`# exit` ile container shell'inden çıkış yapalım.

`$ sudo docker ps -l` son çalışan container'ı gösterecektir:

```
CONTAINER ID        IMAGE                         COMMAND             CREATED             STATUS                     PORTS               NAMES
5b9946c11688        kalilinux/kali-linux-docker   "/bin/bash"         2 days ago          Exited (2) 7 seconds ago                       elegant_varahamihira
```

Üzerinde halihazırda değişiklik yaptığım container'ın id'si 5b9946c11688'miş. Bunu kalilinux:top10 isim:tag'i ile kaydetmek için `sudo docker commit 5b9946c11688 kalilinux:top10` komutunu kullanıyorum.

`$ sudo docker images` bana kayıtlı image'ları gösterecektir:

```
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
kalilinux                     top10               777692e2eac0        13 seconds ago      5.28GB
hello-world                   latest              05a3bd381fc2        6 weeks ago         1.84kB
kalilinux/kali-linux-docker   latest              8ececeaf404d        8 months ago        1.56GB
```

Görebileceğiniz üzere yeni oluşturduğumuz image 5GB+ yer kaplıyor. Daha önce girdiğimiz container'a bağlanmak için `$ sudo docker start -i 5b9946c11688` komutunu kullanabiliriz. -i argümanı interactive bağlantı için kullanılıyor, dolayısıyla şu anda kali container'ına bağlanmış ve root@5b9946c11688:/# prompt'unu görüyor olmalıyız.

```
root@5b9946c11688:/# nmap -V
Nmap version 7.60 ( https://nmap.org )
...
```

Ancak yeni oluşturduğumuz image'ı çalıştırmak istiyorsak `docker run` komutunu kullanmalıyız.

```
$ sudo docker run -i -t kalilinux:top10 /bin/bash
root@dd98641a7356:/# nmap
Nmap 7.60 ( https://nmap.org )
...
root@dd98641a7356:/# exit
```

```
$ sudo docker ps -a
CONTAINER ID        IMAGE                         COMMAND             CREATED             STATUS                            PORTS               NAMES
dd98641a7356        kalilinux:top10               "/bin/bash"         16 seconds ago      Exited (255) 5 seconds ago                            peaceful_feynman
5b9946c11688        kalilinux/kali-linux-docker   "/bin/bash"         2 days ago          Exited (127) About a minute ago                       elegant_varahamihira
e8811fb35a30        hello-world                   "/hello"            2 days ago          Exited (0) 2 days ago                                 hopeful_fermat
```

Görülebileceği üzere kalilinux:top10 imajından yeni bir container oluşturup buna bağlanabildik. Ve bağlandığımızda nmap yüklüydü.

# Sonuç

Sonuç olarak internetten edindiğimiz bir imajın üzerinde geçici ve kalıcı değişiklikler yapabildik. Kalıcı değişiklikleri yeni bir image'da saklayıp istediğimiz zaman bu image'dan yeni bir container oluşturabildik.
