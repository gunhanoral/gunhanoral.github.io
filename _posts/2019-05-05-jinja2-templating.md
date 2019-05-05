---
layout: post
title:  "Jinja2 Şablonları"
description: "Jinja2 ile çeşitli şablonlar oluşturup bunları network configleri için kullanacağız"
date:   2019-05-05 17:03:21
categories: python
tags:
- Python
- Jinja2
image: no
published: true
---

# Giriş

Öncelikle bir özet ile başlayalım. Jinja2 çeşitli şablonlar üretmemize yarayan bir Python kütüphanesidir. Biz de bu şablonları network cihazlarının configlerini oluşturmada kullanacağız. Böylece;
- büyük config dosyaları ile uğraşmaktansa sadece değişkenlerle oynayacağız,
- cihazlar belli bir şablondan çıkmış olacağı için unutulmuş kısımlar olmayacak,
- değişiklikleri cihazlara uygulamak kolaylaşacak.

Bu amaçlarımıza Python ve Python'ın Jinja2 modülü yardımıyla ulaşacağız. Eğer sisteminizde Python yoksa [buradan indirebilir]('http://www.python.org/download'), Jinja2 eksikse de `pip install jinja2` komutunu çalıştırarak yükleyebilirsiniz.

# Şablonlar

Hızlıca başlayalım. Aşağıda cihazımıza loopback interface'i ayarlamak için bir template/şablon kullanıyoruz.

```
from jinja2 import Template

# Şablonumuzu tanımlıyoruz
config = """interface loopback0
exit"""

template = Template(config)

# Ekrana yazdıralım
print(template.render())
```

Yukarıdaki kodu çalıştırırsanız ekrana "interface loopback0" ve "exit" yazdırdığımızı göreceksiniz. Çok başarılı bir uygulama olmadı, değil mi? Bunu gerçekten de kopyala + yapıştır ile her cihazımıza girebiliriz. O zaman değişkenlerden bahsedelim biraz.

# Değişkenler

Bu kısımda şablonda belirlediğimiz alanlara istediklerimizi yazdıracağız. Örneğin:

```
from jinja2 import Template

# Şablonumuzu tanımlıyoruz
config = """interface loopback0
ip address {{ ip_address }} {{ netmask }}
exit
"""
template = Template(config)

# Şablonumuzun içini değişkenlerle doldurup ekrana yazdırıyoruz
print(template.render(ip_address = '1.2.3.4', netmask = '255.255.255.0'))
```

Aldığımız çıktı aşağıdaki gibi olacaktır:
```
interface loopback0
ip address 1.2.3.4 255.255.255.0
exit
```

Farkettiyseniz çift tırnak (süslü) parantez ile belirttiğimiz noktaların içini doldurduk. Jinja2'de değişkenler bu şekilde tanımlanıyor; `{{ degisken_adi }}`. Daha sonra da değişkenlere verdiğimiz değerlerle bu alanların içini dolduruyoruz.

Yeni bir şey deneyelim:

```
from jinja2 import Template

# Şablonumuzu tanımlıyoruz
config = """interface loopback0
ip address {{ ip_address }} {{ netmask }}
exit
"""
template = Template(config)

# Değişkenlerimizi tanımlıyoruz
degiskenler = {
'ip_address': '2.3.4.5',
'netmask': '255.255.255.255'
}

# Şablonumuzun içini değişkenlerle doldurup ekrana yazdıralım
print(template.render(**degiskenler))
```

Süper, her şey güzel gidiyor. Ancak sadece bir cihaz için bir interface tanımladık. Bu kadarla kısıtlı kalmayacak herhalde, değil mi?

```
from jinja2 import Template

# Şablonumuzu tanımlıyoruz
config = """! ### {{ device_name }} için config ###
interface loopback0
ip address {{ ip_address }} {{ netmask }}
exit
! ### {{ device_name }} için config ###

"""
template = Template(config)

# Değişkenlerimizi tanımlıyoruz
devices = [
{'device_name': 'R1',
'ip_address': '2.3.4.5',
'netmask': '255.255.255.255'
},
{'device_name': 'R2',
'ip_address': '22.33.44.55',
'netmask': '255.255.255.0'
}
]
# Şablonumuzun içini her bir cihaz için değişkenlerle doldurup ekrana yazdıralım
for device in devices:
    print(template.render(**device))
```

# Döngüler

Her cihaz sadece bir adet interface'e sahip olmayacak, değil mi?

```
from jinja2 import Template

# Şablonumuzu tanımlıyoruz
config = """! ### {{ device_name }} için config ###
hostname {{ device_name }}
{% for interface in interfaces %}
interface {{ interface.name }}
description {{ interface.description }}
ip address {{ interface.ip_address }} {{ interface.netmask }}
exit
{% endfor %}
! ### {{ device_name }} için config ###

"""
template = Template(config)

# Değişkenlerimizi tanımlıyoruz
devices = [
{'device_name': 'R1',
'interfaces': [
	{
	'name': 'Loopback0',
	'description': 'IGP Loopback',
	'ip_address': '10.1.1.1',
	'netmask': '255.255.255.255'
	},
	{
	'name': 'Loopback10',
	'description': 'TEST LOOPBACK',
	'ip_address': '10.10.10.0',
	'netmask': '255.255.255.0'
	}]
},
{'device_name': 'R2',
'interfaces': [
	{
	'name': 'Loopback0',
	'description': 'IGP Loopback',
	'ip_address': '10.2.2.2',
	'netmask': '255.255.255.255'
	}]
},
{'device_name': 'R3'}
]
# Şablonumuzun içini her bir cihaz için değişkenlerle doldurup ekrana yazdıralım
for device in devices:
    print(template.render(**device))
```

Değişken yapısını değiştirdim. Artık her bir cihazın altında "interfaces" isimli bir list daha var ve bu dictionary interface değişkenlerinin olduğu birer dictionarylerden oluşuyor.

`{% for interface in interfaces %}` ve `{% endfor %}` arasındaki kısım değişkenlerimizdeki her bir interface için derlenecek. R1 cihazı 2 interface'e sahip olduğu için bu iki interface'in configleri derlenip yazdırıldı, R2'de 1 interface vardı, R3'te ise hiç interface olmadığı için ekrana interface ile ilgili hiç bir şey yazdırılmadı:

```
! ### R1 için config ###
hostname R1

interface Loopback0
description IGP Loopback
ip address 10.1.1.1 255.255.255.255
exit

interface Loopback10
description TEST LOOPBACK
ip address 10.10.10.0 255.255.255.0
exit

! ### R1 için config ###

! ### R2 için config ###
hostname R2

interface Loopback0
description IGP Loopback
ip address 10.2.2.2 255.255.255.255
exit

! ### R2 için config ###

! ### R3 için config ###
hostname R3

! ### R3 için config ###
```

# Şartlı durumlar

Son olarak şartlı durumları inceleyelim. Her interface bir description'a sahip olmayabilir. Ya da bazı interface'ler ospf'e dahil edilirken bazıları edilmeyecektir. Template'imizi bu durumlara göre uyarlayalım.

```
from jinja2 import Template

# Şablonumuzu tanımlıyoruz
config = """! ### {{ device_name }} için config ###
hostname {{ device_name }}
{% for interface in interfaces %}
interface {{ interface.name }}
{% if interface.description %}description {{ interface.description }}{% else %}description ### NO DESC ###{% endif %}
{% if interface.ospf %}ip ospf 1 area 0{% endif %}
ip address {{ interface.ip_address }} {{ interface.netmask }}
exit
{% endfor %}
! ### {{ device_name }} için config ###

"""
template = Template(config)

# Değişkenlerimizi tanımlıyoruz
devices = [
{'device_name': 'R1',
'interfaces': [
	{
	'name': 'Loopback0',
	'description': 'IGP Loopback',
	'ip_address': '10.1.1.1',
	'netmask': '255.255.255.255',
	'ospf': True
	},
	{
	'name': 'Loopback10',
	'description': 'TEST LOOPBACK',
	'ip_address': '10.10.10.0',
	'netmask': '255.255.255.0'
	}]
},
{'device_name': 'R2',
'interfaces': [
	{
	'name': 'Loopback0',
	'description': 'IGP Loopback',
	'ip_address': '10.2.2.2',
	'netmask': '255.255.255.255',
	'ospf': True
	}]
},
{'device_name': 'R3',
'interfaces': [
	{
	'name': 'Loopback0',
	'ip_address': '10.3.3.3',
	'netmask': '255.255.255.255',
	'ospf': True
	}]
},
]
# Şablonumuzun içini her bir cihaz için değişkenlerle doldurup ekrana yazdıralım
for device in devices:
    print(template.render(**device))
```

Çıktıda görebileceğiniz üzere R1'in Loopback10 interface'inde ospf komutu yok, R3'ün Loopback0'ında description girmediğimiz için şablondaki description ile ilgili şartlı durumun "else" kısmı çalışmış ve description olarak "NO DESC" yazılmış.

```
! ### R1 için config ###
hostname R1

interface Loopback0
description IGP Loopback
ip ospf 1 area 0
ip address 10.1.1.1 255.255.255.255
exit

interface Loopback10
description TEST LOOPBACK

ip address 10.10.10.0 255.255.255.0
exit

! ### R1 için config ###

! ### R2 için config ###
hostname R2

interface Loopback0
description IGP Loopback
ip ospf 1 area 0
ip address 10.2.2.2 255.255.255.255
exit

! ### R2 için config ###

! ### R3 için config ###
hostname R3

interface Loopback0
description ### NO DESC ###
ip ospf 1 area 0
ip address 10.3.3.3 255.255.255.255
exit

! ### R3 için config ###
```

# Sonuç

Jinja2 template'lerini cihazlarınızı belli bir standarta oturtmak için kullanabilirsiniz. Ayrıca cihaz config'i oluşturmak için büyük kolaylık sağladığı gibi hataları da azaltacaktır (Aaa ben o cihaza snmp config'i girmemiş miyim? tüh >.> ). Bunların yanısıra eğer Ansible veya benzeri bir configuration management tool'u kullanıyorsanız template'i değiştirmeniz ve bunu cihazlarınız üzerinde kullanmanız tüm network config'ini değiştirmenize olanak tanıyacaktır. Büyük kolaylık sağladığı gibi büyük hatalara da yol açabilir, dikkatli olun.
