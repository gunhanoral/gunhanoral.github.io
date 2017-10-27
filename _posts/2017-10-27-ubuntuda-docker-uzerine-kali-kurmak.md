---
layout: post
title:  "Ubuntu'da Docker üzerinde Kali Linux çalıştırmak"
description: "Her zaman Kali'ye ihtiyaç olmuyor ama gerektiğinde de kolayca bulmak zorlaşıyor. Kimileri RaspPI üzerinde Kali çalıştırırken kimileri VM üzerinde çalıştırmayı tercih edebiliyor. Ben Docker tercih ettim. Şimdilik fena gözükmüyor."
date:   2017-10-27 20:08:15
categories: security
tags: ['security', 'kali', 'linux', 'docker']
image: assets/article_images/2017-10-27-ubuntuda-docker-uzerine-kali-kurmak/goddess-kali.jpg
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

Ancak yükleyeceğiniz tüm programlar siz shell'den çıkınca Docker kapanacağı için kaybolacaktır. Gerekli programları listeleyip buna uygun bir Docker imajı oluşturmak gerekecek. Hadi bu da başka bir yazının konusu olsun.
