---
layout: post
title:  "Fortigate ve SIP güvenliği"
description: "Fortigate 5.2.x sürümü ve sonrasında SIP Helper default olarak aktif hale getirildi. Bu değişiklik konudan habersiz bir çok SIP sunucusunu SIP saldırılarına hedef yaptı."
date:   2017-05-04 23:19:42
categories: Security
tags:
  - Security
  - SIP
  - Fortigate
  - SIP ALG
  - SIP Helper
  - SIP Attack
image: /assets/article_images/2015-05-04-sip-guvenligi/Phoneslarge.jpg
published: true
---

# Yapı

Varsayalım firewall'unuz arkasında yer alan bir SIP PBX, Firewall'un untrust tarafındaki servis sağlayıcınız ile SIP konuşuyor. Güvenliğiniz için yapmanız gerekeni yaptınız ve sadece servis sağlayıcının IPlerine SIP portlarından (ve rtp portları tabi ki) izin verdiniz, geri kalan sip/rtp trafiğini blokladınız.

# Problem

Problem ise buna rağmen dışarıdan gelen SIP mesajlarının PBX'e ulaşması. Bilmediğiniz yerlerden gelen SIP mesajlarına PBX'iniz 401 veya 403 gibi yanıtlar dönüyor olabilir ancak bu yine de bir güvenlik açığıdır. Bozulmuş birkaç INVITE, yarı bırakılan sessionlar ve daha nice saldırı çeşitleri PBX'e zarar verebilir.

## Troubleshoot

Debug aldığınızda aşağıdaki loglarla birlikte ummadığınız ip'lerin baş yardığını görebilirsiniz:

> id=12345 trace_id=54321 func=\__ip_session_run_tuple line=2657 **msg="run helper-sip(dir=original)"**

Bir de elimin altında [SIPp][sipp] kullanabileceğim bir cihaz (Kali Linux) vardı. SIPp ile gönderdiğim mesajlar PBX tarafından yanıtlanıyordu.

# Çözüm

İlk önce ana kaynak: [Handbook - VoIP Solutions: SIP][handbook]

Çözüm beklediğim kadar kolay olmadı. SIP ALG ve SIP Helper disable edilirse -zaten kullanılması için bir sebep yoktu- hemen çözülür diye düşünmüştüm. Sonuçta sorunu yaratan SIP Helper'dı. İlk önce dur bir deneyeyim diye SIP ALG'nin kapalı olduğu bir voip profile oluşturdum ve ilgili policy'de voip profile olarak bu yeni oluşturduğum profili kullandım. Tabi ki işe yaramadı.

```
config voip profile
  edit VoIP_Pro_2
    config sip
    set status disable
  end
```

Daha sonra [burada][dissip] bahsedildiği şekilde sip helper'ı disable edip sildim (RTP adımına gerek yoktu). Ancak yüzlerce cihazın bağlı olduğu cihazı tabi ki yeniden başlatmadım.

```
Step One: Disable SIP Helper!

1.config system settings
2.set  sip-helper disable
3.set sip-nat-trace disable
4.REBOOT THE DEVICE!!! (You may want to wait on rebooting until AFTER you do the next few steps!)

Step Two: Delete the SIP Helper

1.config system session-helper
2.show (Look for the SIP helper, often object 12)
3.delete # (whatever number the SIP helper was)
```
En son baktım olmuyor...

```
# diagnose sys session filter [ip / port bilgisi]
# diag sys session list
# diag sys session clear
```

Umutsuzca son bir kez SIPp'yi çalıştırdım. Amacım log almakken SIPp'nin cevap alamadığını görünce yaptıklarımın _sonunda_ işe yaradığını anladım. Güzel ve uğraştırıcı bir deneyimdi. İnternette benzer bir konu bulmak zor olduğu için paylaşıyorum.  

[handbook]:	[http://docs.fortinet.com/uploaded/files/2813/fortigate-sip-54.pdf]
[dissip]:	[http://www.pkguild.com/2015/08/how-to-disable-sip-and-rtp-processing-on-your-fortigate-for-voip-goodness/]
[sipp]:	[http://sipp.sourceforge.net/]
