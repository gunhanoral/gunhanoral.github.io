---
layout: post
title:  "Python ile resim boyutu ölçeklendirmek"
date:   2017-02-27 22:42:25
categories: jekyll update
tags: featured, python, image
image: /assets/article_images/2014-08-29-welcome-to-jekyll/desktop.JPG
---
Siteyi yapılandırırken fotoğrafımın boyutlarını ölçeklendirmem gerekti. Elimin altında o esnada uygun bir program bulunmadığı için Python ile bunu yapıp yapamayacağımı merak ettim. Kısa bir google aramasından sonra [bu siteye][resize] ulaştım. Burada yer alan ilk kod parçasında basit aritmetik işlemlerle istenen genişlikte, oranlı bir resim elde edilebiliyor.

{% highlight python %}
from PIL import Image
basewidth = 300
img = Image.open(‘fullsized_image.jpg’)
wpercent = (basewidth / float(img.size[0]))
hsize = int((float(img.size[1]) * float(wpercent)))
img = img.resize((basewidth, hsize), Image.ANTIALIAS)
img.save(‘resized_image.jpg’)
{% endhighlight %}

Eğer yükseklik bilgisi üüzerinden ilerlemek gerekirse aşağıdaki kod kullanılabilir:

{% highlight python %}
baseheight = 560
img = Image.open(‘fullsized_image.jpg’)
hpercent = (baseheight / float(img.size[1]))
wsize = int((float(img.size[0]) * float(hpercent)))
img = img.resize((wsize, baseheight), Image.ANTIALIAS)
img.save(‘resized_image.jpg’)
{% endhighlight %}

[resize]:      https://opensource.com/life/15/2/resize-images-python
