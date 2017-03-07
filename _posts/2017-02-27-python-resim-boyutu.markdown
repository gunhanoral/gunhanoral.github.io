---
layout: post
title:  "Python ile resim boyutu ölçeklendirmek"
date:   2017-02-27 22:42:25
categories: python
tags:
  - python
  - image
  - resize
image: /assets/article_images/2014-08-29-welcome-to-jekyll/desktop.JPG
---
Siteyi yapılandırırken fotoğrafımın boyutlarını ölçeklendirmem gerektiğinde bu kadar temel bir işlemi yapabileceğim program bulamamak beni ufaktan afallattı. Sonra da hazır elimin altında Python var diyip Python ile bunu yapıp yapamayacağımı görmek istedim. Kısa bir google aramasından sonra [bu siteye][resize] ulaştım. Burada yer alan ilk kod parçasını kullanarak basit aritmetik işlemlerle istenen genişlikte, oranlı bir resim elde edilebiliyor.

```python
from PIL import Image
basewidth = 300
img = Image.open(‘fullsized_image.jpg’)
wpercent = (basewidth / float(img.size[0]))
hsize = int((float(img.size[1]) * float(wpercent)))
img = img.resize((basewidth, hsize), Image.ANTIALIAS)
img.save(‘resized_image.jpg’)
```

Eğer yükseklik bilgisi üzerinden ilerlemek gerekirse aşağıdaki kod parçası kullanılabilir:

```python
baseheight = 560
img = Image.open(‘fullsized_image.jpg’)
hpercent = (baseheight / float(img.size[1]))
wsize = int((float(img.size[0]) * float(hpercent)))
img = img.resize((wsize, baseheight), Image.ANTIALIAS)
img.save(‘resized_image.jpg’)
```

Hatta:

```python
def resimize(so, de, si):
    basewidth = si
    img = Image.open(so)
    wpercent = (basewidth / float(img.size[0]))
    hsize = int((float(img.size[1]) * float(wpercent)))
    img = img.resize((basewidth, hsize), Image.ANTIALIAS)
    img.save(de)
```

[resize]:      https://opensource.com/life/15/2/resize-images-python
