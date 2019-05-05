---
layout: post
title:  "Network Mühendisleri için Ansible"
description: "Network cihazlarının yönetimini kolaşlaştırmak için Ansible'ı nasıl kullanabiliriz? Bu yazıda hem kendi deneyimlerimi paylaşıyorum, hem de internette bulduğum ve beğendiğim kaynakları topluyorum."
date:   2018-06-19 09:00:00
categories: network
tags:
  - ansible
  - automation
  - playbooks
  - network engineer
  - network automation
  - network orchestration
  - featured
image: /assets/article_images/2017-09-16-ansible-notlari/Ansible_logo_wordmark.jpg
published: false
---
# Otomasyon hakkında

## Neden otomasyon?

> Ya da CLI neyinize yetmiyor?

Artık tekrarlı işlerde otomasyona alışıp CLI'da hızlıca bir şeyler yapmayı unuttuğumdan mıdır nedir, geçen hafta altı tane cihazın her birine üç beş satırlık snmp konfigürasyonu girmem yarım saatimden fazlasını aldı. Benim yavaşlığım tabi ki bir etmen ama kendimi savunmak adına bağlandığım MSANların aşırı yavaş çalıştığını, write'ın bir dakikadan fazla zaman aldığını söylemek istiyorum. 

Ama ne gerek var ki? Yani aynı konfiği 6 ya da 60 cihaza girmek bana bir şey katmıyor. Neden hepsiyle tek tek uğraşayım? Ona ayıracağım vakti diğer network işlerine ayırmak, kendimi geliştirmekle harcamak daha anlamlı geliyor.

Aslında otomasyon yapılmamasının nedeni belli; **ya bir sorun çıkarsa?** SNMP'de bir kesinti hizmet kesintisi yaratmaz hadi ama interface / port tanımlarındaki değişiklikler? Bir MSAN'dan yüzlerce müşteri bağlanıyor. Bir router'dan sayısız trafik geçiyor. Network mühendisi kontrolü altında olmayan bir değişikliğin etkilerini hiç bir yönetici düşünmek dahi istemez. 

Mühendislik bir trade-off'tur, denir (Trade-off: ödünleşim). Otomasyonda da insan hatasını ve yavaşlığını minimuma indirirken... neyi artırıyoruz? Riski mi? Gelin biraz daha detaya inelim.

## CLI ve otomasyon

CLI - Command Line Interface insanlar için üretilmiş bir arayüzdür. Girilen her komut, komutlara verilen her yanıt insan faktörü göz önünde bulundurularak üretilmiştir. Hal böyle olunca otomasyon için kullanılan sistemlerin CLI'a uyumlu hale getirilmesi biraz sıkıntılı bir süreç oluyor. 

Json, xml gibi formatlar bilgisayarlar için daha uygun. Bu nedenle bir çok network firması cihazlarına bu formatları kullanarak erişmenizi sağlayan API - Application Programming Interface koyuyor. Ben de Fortigate'ime bir isim - ip listesi verip yüzlerce adres oluşturabiliyorum. Mis gibi işlem. 

Netconf yine çok kullanılan ve işlevsel bir konfigürasyon metodu. SSH üzerinden cihazla iletişime geçerek XML formatında veri alışverişi yapıyor. 

REST kullanan API'ler bence daha güzel. Json daha okunabilir bir formatta olduğu için bana daha rahat geliyor. REST, web portlarını kullanarak işlem yapmanıza olanak sağlıyor. 

## Ansible ne yapıyor peki?

Ansible uzaktaki cihazın üzerindeki Python'ı kullanarak çeşitli işlemler yapıyor. İster shell komutu çalıştıracak olalım, ister çeşitli config dosyası değiştirelim, aksi belirtilmedikçe bu şekilde çalışıyor. Netwo
