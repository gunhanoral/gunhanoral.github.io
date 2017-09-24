---
layout: post
title:  "Ansible'da özelleştirilmiş dinamik envanter"
description: "Ansible'da özelleştirilmiş dinamik envanter nasıl hazırlanır?"
date:   2017-09-24 15:13:28
categories: network
tags: ['network', 'python', 'ansible', 'network automation', 'network orchestration', 'featured']
image: /assets/article_images/2017-09-16-ansible-notlari/Ansible_logo_wordmark.jpg
published: true
---
# Ansible Özelleştirilmiş Dinamik Envanter

## Dinamik envanter?

Ansible host dosyasını sistemlerimizdeki her değişiklikte güncelleyeceksek işimiz iş demektir. Ancak Ansible bizim için bu durumu da öngörmüş ve [çeşitli imkanlar sağlamış](http://docs.ansible.com/ansible/latest/intro_dynamic_inventory.html). Tabi biz her zaman AWS (ya da benzeri bir yapı) ile çalışacak değiliz.

## Özelleştirilmiş?

Özelleştirilmişten kasıt kendi üretimimiz, el emeğimiz, python scriptimiz (ya da istediğiniz bir dil) olacak. Aslında çok basit, Ansible envanter olarak spesifik bir formata uygun json çıktısı bekliyor. [Jeff Geerling bloğunda](https://www.jeffgeerling.com/blog/creating-custom-dynamic-inventories-ansible) bu beklentiyi karşılamanın yolunu şöyle göstermiş:
```
{
    "group": {
        "hosts": [
            "192.168.28.71",
            "192.168.28.72"
        ],
        "vars": {
            "ansible_ssh_user": "johndoe",
            "ansible_ssh_private_key_file": "~/.ssh/mykey",
            "example_variable": "value"
        }
    },
    "_meta": {
        "hostvars": {
            "192.168.28.71": {
                "host_specific_var": "bar"
            },
            "192.168.28.72": {
                "host_specific_var": "foo"
            }
        }
    }
}
```
Sonra bu basit iş için aşırı kompleks bir kod önerisinde bulunmuş - ki bence hiç gerek yok. Biz daha sadesini üreteceğiz. Unutmayın, ihtiyacımız yukarıdaki gibi bir çıktı almak.

``` python
#!/home/gunhan/anaconda3/bin/python
from collections import defaultdict
import pandas as pd
import json

# envantere ait bilgiler
df = pd.DataFrame(
	{'equipip': {0: '10.0.0.1', 1: '10.0.0.2', 2: '10.0.0.3', 3: '10.0.0.4', 4: '10.0.0.5'},
	'username': {0: 'admin', 1: 'admin', 2: 'root', 3: 'admin', 4: 'admin'},
	'pwd': {0: 'Hu@w31', 1: 'Actelis1', 2: 'ETZ', 3: 'Hu@w31', 4: 'Hu@w31'},
	'hostname': {0: 'sw01', 1: 'dslam01', 2: 'msan01', 3: 'sw02', 4: 'sw03'},
	'vend': {0: 'Huawei', 1: 'Actelis', 2: 'ZTE', 3: 'Huawei', 4: 'Huawei'}}
)

# bir iki döngü ile sözlüğü dolduruyoruz

env = defaultdict(dict)
for make in df.vend.unique():
        env[make]['hosts'] = df[df.vend == make].equipip.tolist()
        env[make]['vars'] = {"ansible_ssh_user": df[df.vend == make]['username'].unique()[0]}
env["_meta"]["hostvars"] = defaultdict(dict)
for ind, row in df.iterrows():
        env["_meta"]["hostvars"][row['equipip']]["password"] = row['pwd']

# sonra da sözlüğü ekrana json formatına uygun bir şekilde yazdırıyoruz
print(json.dumps(env))
```

Yukarıdaki örnekte _pandas_ kullanmamın sebebi cihazlarla ilgili bilgileri bir yerden (örn _excel_ dosyası ya da _sql_ sorgusu) çektiğimizi farzetmem. DataFrame içeriği sql ile çekilmiş veriler de olabilirdi:

`pd.read_sql("select top 10 * from devicetable", pymssql.connect(*args))`

df içeriği aşağıdaki gibi gözüküyor:

```
>>> df
    equipip hostname       pwd username     vend
0  10.0.0.1     sw01    Hu@w31    admin   Huawei
1  10.0.0.2  dslam01  Actelis1    admin  Actelis
2  10.0.0.3   msan01       ETZ     root      ZTE
3  10.0.0.4     sw02    Hu@w31    admin   Huawei
4  10.0.0.5     sw03    Hu@w31    admin   Huawei
```

Burada önemli olan envanter dosyası çalıştırıldığında yeni bilgileri alıp sözlük içine atması.

Scripti yazdıktan sonra dosyanın çalıştırılabilir olmasına dikkat edelim. `chmod +x inventory.py`

Sonra da ansible'ı çalıştıralım:

`/usr/bin/ansible -i inventory.py Huawei -m ping`

Sonuç:

```
10.0.0.4 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: ssh: connect to host 10.0.0.4 port 22: Connection timed out\r\n",
    "unreachable": true
}
10.0.0.5 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: ssh: connect to host 10.0.0.5 port 22: Connection timed out\r\n",
    "unreachable": true
}
10.0.0.1 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: ssh: connect to host 10.0.0.1 port 22: Connection timed out\r\n",
    "unreachable": true
}
```
