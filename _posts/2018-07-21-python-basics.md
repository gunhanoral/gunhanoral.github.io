---
layout: post
title:  "Python Temelleri"
description: "Python'a bir giriş"
date:   2018-07-21 12:51:32
categories: python
tags: ['python', 'basics']
image: assets/article_images/2017-08-23-gns3-vm-kurulumu/gns3.png
published: false
---

Network mühendisliğinde Python kullanmak isteyenler için bir başlangıç oluşturacak, gerektiğinde de göz atılacak bir yazı dizisi yazmak istedim. Umarım işinize yarar, takıldığınız yerde de soru sorarsınız.

# Ne şimdi bu?

İlk olarak Python'a bakış açınızı ele alalım. Python ile ilgili bir şeyler anlatmaya çalıştığım çoğu network mühendisinin ilk sorusu "Ne işe yarıyor şimdi bu?" oldu. Bu soruya cevabım şu şekilde: "Python, diğer programlama dilleri gibi, bir bilgisayara istediğinizi yaptırmaya yarayan esnek bir dil". Çok teorik ve çok doğru bir bakış açısı olmayabilir ama ben yaklaşımınızın bu doğrultuda olmasını makul buluyorum. Bazı işlemler yaptırmak için bilgisayarın anlayacağı dilden konuşmak. 

Peşinden gelen ikinci soru da şu: "Peki ama ne işe yarıyor, ne yapabiliyorum yani bununla?" 

Az önce söylediklerimi bir kez daha tekrarlıyorum bu sefer de. Çünkü beklentileri hesap makinesi, router, switch gibi bir şey. Daha genel amaçlı düşünmemiz gerekiyor. İster basit aritmetik işlemler olsun, ister kolayca router config'i üretmek olsun, ister binlerce cihaza birkaç dakikada bağlanıp çeşitli işlemler yapmak olsun; hatta network mühendisliğini bırakıp web sitesi yapalım, bilgisayar oyunu yapalım, envanter takip çizelgesi oluşturalım. Nasıl isterseniz, elimizde bunun için yeterince alet edevat var. Ama biz network mühendisliğine odaklanacağız.

# Niye ama?

Network mühendislerinin genelde sormadığı ama benim yanıtlamak istediğim bir soru var. O da "neden Python?" sorusu. 

Cevabı da basit ve öğrenmesi kolay bir dil olması. Bu kadar. Basit bir dil. Kompleks işlemler yapabiliyor ama ekranda gördüğünüz kodları okuması ve yazması kolay. Kafanızı karıştıracak simgeler az ve anlaşılır. Mesela `"net" in "network"` yazıyorsunuz, `True` diyor. Çünkü "network" kelimesinin içinde "net" karakter dizisi var. `"engineer" in "network"` yazın, `False` yanıtını alırsınız. Çünkü "network"'ün içinde "engineer" kelimesi geçmiyor. Basit, anlaşılabilir.

Basit olunca öğrenen çok olmuş. Öğrenen çok olunca da "Dur database'e bağlanma işini kolaylaştırayım", "ssh bağlantısı için modül yazayım", "Excel-vari işlemler yapmak gerekiyor, bunun için yazdığım modülü diğer insanlarla paylaşayım" diyen sayısı çok. Zaten open source mantığıyla yola çıkılmış, herkes bir şeyler paylaşıyor. Python komünitesi gitgide büyüyor. Network mühendisleri de zor bir dil öğrenmekle vakit harcamak istemediği için network ile ilgili modüller de Python'da bol bol var. İsterseniz siz de modül yazıp bu komüniteyle paylaşabilirsiniz.

# Önereceğin kaynaklar var mı?

Ben Python'ı İstihza'nın tutoriallarından öğrendim. Zamanında bu tutorialları kitaplaştırdı (aslında sanırım o kitaplaştırmadı ama uzun hikaye) daha sonra da bildiğim kadarıyla özel işlerine vakit ayırmak için sitesini sonlandırdı. Dökümanlar hala internette bulunabilir. Diğer kaynaklarla beraber aşağıda paylaşıyorum.

# Neler öğreneceğiz? 

Konu başlıkları aşağıdaki gibi:

- Integer
- String
- List
- Set
- Dictionary
