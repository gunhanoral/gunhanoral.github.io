---
layout: post
title:  "Ansible Notları"
description: "Birkaç gün önce test amaçlı Ansible kurulumu yaptım ve çeşitli denemeler yapmaya başladım. Deneyimlerim ile ilgili notları burada tutuyorum."
date:   2017-09-16 17:42:38
categories: python
tags: ['network', 'python', 'ansible', 'network automation', 'network orchestration']
image: /assets/article_images/2017-09-16-ansible-notlari.md
published: true
---
# Ansible Nedir?

[Ansible](https://www.ansible.com/) "Simple IT Automation" mottosuyla çıkan IT otomasyonunu kolaylaştıran (ve hatta automation harici orchestration da yapabilen) Python temelli bir yazılım. Ansible sayesinde birden fazla cihaza bağlanıp basit komutları çalıştırabileceğimiz gibi, kompleks senaryoları da gerçekleştirebiliyoruz. Şimdiye kadar yazdığım en kompleks *playbook* uptime'ı 300 günün üzerindeki linux cihazları tespit etmek olsa da zamanla daha iyi şeyler yapabileceğimi düşünüyorum. Ansible'ın en büyük artısı [modülleri](http://docs.ansible.com/ansible/latest/modules_by_category.html). Genellikle firmaların kendi cihazları için çıkarttığı modüller sayesinde bu cihazların yönetimi çok kolaylaşıyor. Örneğin Cisco'nun iosxe, iosxr, asa, aci için çıkarttığı modüller sayesinde hem yapılan işlemler kontrol altında tutuluyor, hem de yapılan işlemlerin çıktıları ekstra emeğe gerek kalmadan okunaklı ve düzgün bir biçimde tutuluyor.

Ansible'ı ben beğendim. Günlük işlerimde otomasyon için kullandığım scriptleri bir kenara bıraktığımı söyleyebilirim.

Şimdilik üç noktaya odaklanalım:
- Inventory
- Ad-hoc commands
- Playbooks

## Inventory - Envanter

Ansible bağlantı sağlayacağı cihazları envanterinden seçiyor. Yani envanterde olmayan bir cihaza bağlanamıyorsunuz. Bir cihazı envantere eklemek çok basit. /etc/ansible/hosts dosyasını açtığınızda zaten syntax'ı anlayacaksınız ama ben yine de bir örnek göstereyim.

```
[finans]
10.0.0.1
10.0.0.2
10.0.0.3

[bt]
10.0.1.1
10.0.1.5

[cisco]
10.0.0.1
10.0.1.1
abc.com
10.0.0.3

[cisco:vars]
ansible_ssh_user=cisco
baska_bir_degisken=ciscocuk
```

Anlayabileceğiniz üzere cisco grubu için özel değişkenler atanabiliyor. Finans grubu için özel değişken ataması yapılacak olsa `[finans:vars]` isminde bir grup tanımlaması yapılabilir.

Bunların haricinde dinamik envanterler de oluşturulabiliyor.

## Ad-Hoc Commands

Bunlar cli üzerinde basit işlemler yapmak için kullanılan komutlardır. Örneğin:

`root@ansible:~# ansible finans -a "uptime"`

Bu komut finans grubundaki cihazlara ssh ile bağlanıp uptime komutunu çalıştıracaktır. Bir iki noktaya dikkat çekeyim:
* SSH bağlantısı için kullanıcı adı girmedik. Şu anda root olarak çalıştığımız için diğer cihaza da otomatik olarak root ile bağlanmaya çalışacaktır. İstersek -u komutu ile kullanıcı adı girebilirizz
* SSH bağlantısı için şifre girmedik. Default olarak kayıtlı key ile bağlanmaya çalışacaktır. İstersek -k ile şifre sormasını sağlayabiliriz.
* Şimdilik odül belirtmedik. Default modül ile işlem yapıyoruz. İstersek -m ile modül belirtebiliriz.

``` bash
root@ansible:~# ansible finans[0] -a "uptime" -u gunhan -k
SSH password:
10.0.0.1 | SUCCESS | rc=0 >>
 02:57:49 up 255 days, 22:46,  2 users,  load average: 0.00, 0.01, 0.05
```

Aşağıdaki örnekteki ping modülü karşı cihaza ssh ile bağlanılıp bağlanılamadığını ve bağlanılan cihazda çalıştırılabilir python modülü olup olmadığını kontrol ediyor. ICMP ping ile karıştırmayınız. Ansible'da bir çok modül karşı uçta çalıştırılabilir bir python sürümü olduğunda (spesifik olarak /usr/bin/python) işlem yapabiliyor. Örneğin yukarıdaki örnekte "uptime" komutunu çalıştıran aslında karşı uçtaki python modülü. Bu nedenle çalıştırdığınız komutun gereksinimlerine dikkat etmeniz önemli.

``` bash
root@quigon:~# ansible finans[0] -m ping
10.0.0.1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

Kullanmak istediğiniz modüllere [bu sayfadan](http://docs.ansible.com/ansible/latest/modules_by_category.html) bakmanızı öneririm.

## Playbooks

Ansible'ın parladığı nokta playbookları. Ad-hoc yapılamayacak kompleks işlemleri playbooklar yardımı ile kolaylıkla... Aslında hiç kolay değil şimdilik ama biraz daha kullanınca kolaylaşmaya başlayacak diye umuyorum. :)

İlk yazdığım playbooklardan birisine göz atalım:

``` yaml
---
- hosts: cisco901[11:15]
  gather_facts: no
  connection: local

  vars_prompt:
  - name: "mgmt_username"
    prompt: "Username"
    private: no
  - name: "mgmt_password"
    prompt: "Password"

  tasks:

  - name: SYS | Define provider
    set_fact:
      provider:
        host: "{{ inventory_hostname }}"
        username: "{{ mgmt_username }}"
        password: "{{ mgmt_password }}"

  - name: IOS | Show clock
    ios_command:
      provider: "{{ provider }}"
      commands:
        - show clock
    register: clock

  - debug: msg="{{ clock.stdout }}"
```

* hosts: envanter dosyamdaki cisco901 grubundaki 11'den 15'e kadarki elemanları seçiyorum ve bu cihazlara bağlanıyorum.
* gather_facts: Normalde Ansible ilk bağlandığında setup modülünü çalıştırıp çeşitli fact'ler elde ediyor. Buna gerek olmadığı için boşuna çalıştırmıyoruz.
* connection: "show clock" komutunu çalıştırmak için ios_command modülünü kullanacağız. Bu modülün gereksinimlerinden birisi connection'ın local olması, diğeri de provider değişkeninin kullanılması. Bu sayede provider değişkenlerindeki değerlerle bağlantı sağlanıyor.
* vars_prompt: Provider içine atılacak kullanıcı adı ve şifre kısımlarının kullanıcıya sorulması sağlanıyor.
* tasks: Bu kısım karşı uçlarda yapılacak işlemleri tanımladığımız kısım. 3 tasks var, ilk ikisine isim verilmiş, üçüncüsü isimsiz kalmış.
  1. SYS \| Define provider: set_fact modülü yardımıyla provider'ın içi dolduruluyor: host, username, password
  1. IOS \| Show clock: ios_command modülünü kullanıyoruz. Provider özellikleri belirtilip "show clock" komutu gönderiliyor. Komutun çıktısı clock isimli değişkene *register* ediliyor.
  1. debug modülü ile clock değişkeni ekrana yazdırılıyor.
