---
layout: post
title:  "GNS3 VM Kurulumu"
description: "GNS3 VM kurarken dikkat edilecek bazı hususlar. Ben uğraştım, siz uğraşmayın. "
date:   2017-08-23 19:33:32
categories: network
tags: ['gns3', 'gns3 vm', 'VirtualBox']
image: 
published: true
---

Windows 10 üzerine GNS3 kurarken bazı dertler, çileler çektim. Biraz kendime not olsun diye, biraz da internette arayıp derdine derman bulamayanlara çözüm olsun diye buraya not düşüyorum. 

# GNS3 

Bildiğiniz üzere GNS3 network cihazlarında kullanılan işletim sistemlerini bilgisayarınızda çalıştırabileceğiniz bir uygulama. Özellikle eğitim amaçlı ve test amaçlı çokça kullanılıyor. CCNA, CCNP konuları için GNS3'te çalışmak standart olmuştur diyebilirim. Bilgisayar özellikleriniz yettiğince ufak bir topoloji oluşturup bu cihazları birbirine bağlayabilir, network üzerinde OSPF, EIGRP, BGPhatta at koşturabilirsiniz. Wireshark ile bu mesajlaşmaları inceleyebilir, bu topoloji üzerinden internete çıkabilirsiniz. Epey kullanışlı ve öğretici bir program. 

## GNS3 indirme ve yükleme

GNS3 kullanmak eskiden daha bir kolaydı sanki. Standart bir kurulum yapar, ardından import ettiğimiz ios'ları çalıştırır, cihazlara bağlanıp konfigürasyona başlardık. Artık kurulumu birazcık daha değişmiş, bir nebze zorlaşmış. Gelin ilk önce GNS3'ün kendi sitesinden GNS3'ü indirelim. İlgili adresteki download butonuna tıkladıktan sonra platformunuza uygun sürümü seçerek indirmeye başlayın. Sanırım üyelik istiyordu, üye olmanızda bir sakınca yok. Sitede birçok konu hakkında bilgi mevcut. 

Sonrası aslında gayet kolay bir kurulum. Next tuşlarına ardı ardına basmanız yeterli olacaktır. Ben Solarwinds gibi ekstra programları yüklemedim. Winpcap'in son sürümü bilgisayarımda halihazırda vardı ancak yine de aynı sürümü yüklemek gerekebiliyormuş, haberiniz olsun. 

### Test

GNS3 kurulduktan sonra çabucak bir test yapalım. Yeni bir proje oluşturup adına test-pcs gibi bir isim verebiliriz. Sol menüden End Devices'ı seçip birkaç tane PC ekleyelim. Bu PC'leri bir hub yardımı ile birbirine bağlayalım. Üst menüden Start All Nodes'a ve ardından Console Connect to All Nodes'a basalım. PC'lere `ip 10.0.0.1 255.255.255.0` ve `ip 10.0.0.2 255.255.255.0` komutlarıyla ip verdikten sonra bir cihazdan diğerine başarılı bir biçimde ping atabiliyor olmalıyız. Sorun yaşanıyorsa GNS3'teki loglara göre inceleme yapmakta fayda var. Ancak genelde sorun olmuyor ya da GNS3'ü engelleyen Antivirus'te uygulamaya izin vererek sorun gideriliyor. 

## GNS3 VM

GNS3 VM gerek güvenlik gerek de hız açısından standart GNS3'e göre avantajlara sahip. Bu avantajları kullanabilmek için bir sanal makine yöneticisine, bir de GNS3 VM imajına sahip olmanız gerekmekte. Vmware Workstation ve Oracle VirtualBox sanal makine yönetimi için iki çözüm. Hangisi ile çalışacağımızı seçtikten sonra GNS3 VM'in ilgili versiyonunu sitesinden indirebiliriz. Ben, kendi bilgisayarımda VirtualBox ile kurulum yaptığım için bu kurulumu anlatacağım.

İlk önce sitesinden VirtualBox'ın son sürümünü indirelim. Dosya inerken ben de size yaşadığım sorunu anlatayım:

### Kurulum öncesi bir ek bilgi

GNS3 her seferinde şöyle bir hata veriyordu: "Error while setting up node: GNS3VM: DHCP must be enabled on VirtualBox host-only network: vboxnet0 for GNS3 VM" Bu hatadan kurtulmak için yaptıklarım:
1. VirtualBox kurulumundan sonra sistemi yeniden başlatmak gerekiyormuş. Kurulum sonrası sorulmadığı için ben es geçmiştim, bu yüzden host-only network'üm hiç oluşmadı. Manuel oluşturma çabalarım da hata verdi.
1. Sonrasında File -> Preferences -> Network -> Host-Only Networks kısmında yer alan ilgili adapter'ı aşağıdaki şekilde konfigüre ettim:
  * IPv4 Address: 192.168.225.1
  * IPv4 Network Mask: 255.255.255.0
  * DHCP Server: Enabled
  * Server Address: 192.168.225.254
  * Server Mask: 255.255.255.0
  * Lower Address Bound: 192.168.225.100
  * Upper Address Bound: 192.168.225.200
1. Bu esnada hala düzelmeyince birkaç defa Windows Network and Sharing Centre'daki adapter'ı disable/enable yaptım ama işe yaramadı. 
1. VirtualBox'ı silip `VirtualBox-5.1.26-117224-Win.exe -msiparams NETWORKTYPE=NDIS5` parametresiyle yeniden kurdum. İşe yaramadı.
1. En son GNS3 VM'i seçip Settings -> Network -> Adapter 1 -> Advanced -> Promiscuous Mode'u Allow All yapınca sorunum giderildi.

Bunu kurulumdan önce belirtiyorum ki belki siz de kurulumu `-msiparams NETWORKTYPE=NDIS5` parametresiyle yapmak istersiniz. Belki diğer adımlar olmadan sadece son adım işe yarar, belki de her biri sorun yaşanmaması için gereklidi, bilemiyorum. Bence normal kurulum yapıp promiscuous modu denemekte fayda var.

### Kuruluma Devam

VirtualBox'u NDIS5 ya da NDIS6 ile kuralım. İndirdiğimiz GNS3 VM'i zip dosyasından çıkartalım ve VirtualBox'a tanıtalım. VM'i çalıştırmaya gerek yok, GNS3'ü konfigüre ettikten sonra GNS açıldığında VM otomatik olarak başlayacak. 

GNS3'te Edit -> Preferences -> VirtualBox VMs -> Add -> Local Computer -> GNS3 VM seçilip eklenmeli.
Aynı preference menusunde GNS3 VM -> Enable GNS3 VM seçildikten sonra Virtualization Engine VirtualBox, Settings altında VM name GNS3 VM seçilmeli. Diğer ayarlar olduğu gibi bırakılabilir.

### Test

Bu noktada bir test daha yapmakta fayda var. Virtual PC eklediğimizde DHCP hatası alıyorsak üstteki adımları inceleyerek sorunu gidermeyi deneyebiliriz. Eğer hata almıyorsak yine iki üç PC'yi bir Hub ile bağlayaıp birbirine eriştirmeyi denemeliyiz. Eğer çalışıyorsa kutlarım, başarılı bir kurulum sağlamışsınız demektir. 
