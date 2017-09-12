---
layout: notebook
title:  "Python 101"
description: "Birkaç gün önce test amaçlı Ansible kurulumu yaptım ve çeşitli denemeler yapmaya başladım. Deneyimlerim ile ilgili notları burada tutuyorum."
date:   2017-08-24 12:42:38
categories: python
tags: ['network', 'python', 'ansible', 'network automation', 'network orchestration']
image:
published: true
---
<center><h1><a href="https://www.python.org/" id='ch0'>Python</a> 101</h1></center>

## Konu Başlıkları:

<ol start="0">
  <li>[Python'ı çalıştırmak](#ch0)</li>
  <li>[Integers, Floats, Strings \*](#ch1)</li>
  <li>[Lists, Tuples, Sets, Dictionaries \*](#ch2)</li>
  <li>[Fonksiyonlara giriş \*](#ch3)</li>
  <li>[If - elif - else koşulları](#ch4)</li>
  <li>[While for döngüleri ve iterasyon](#ch5)</li>
  <li>[Modüllere giriş \*](#ch6)</li>
  <li>[Dosya işlemleri](#ch7)</li>
  <li>[Try - Except - Finally](#ch8)</li>
  <li>[Telnet vs SSH](#ch9)</li>
  <li>[SNMP](#ch10)</li>
  <li>[Database bağlantısı](#ch11)</li>
  <li>[HTTP ve API](#ch12)</li>
</ol>

## Kaynaklar:

Network Programmability:
  - [Cisco DevNet](https://developer.cisco.com/site/devnet/home/index.gsp)
  - [Cisco DevNet Learning Labs](https://learninglabs.cisco.com/)
  - [Cisco Live - DEVNET-1040 - Introduction to Python Network Programming for Network Architects and Engineers](https://www.ciscolive.com/online/connect/sessionDetail.ww?SESSION_ID=95921&tclass=popup)
  - [Cisco Live - DEVNET-1725 - How to Be a Network Engineer in a Programmable Age](https://www.ciscolive.com/online/connect/sessionDetail.ww?SESSION_ID=95969&tclass=popup)
  - [A Network Engineer’s Journey in Programmability](https://learningnetwork.cisco.com/blogs/community_cafe/2017/03/27/a-network-engineer-s-journey-in-programmability)
  - [Kevin Wallace - Intro to Python](https://kwallaceccie.mykajabi.com/blog/introduction-to-python-for-cisco-network-professionals)

Python:
  - [Python](https://www.python.org/)
  - [Built-in Functions](https://docs.python.org/3/library/functions.html)
  - [İstihza](https://belgeler.yazbel.com/python-istihza/)
  - [Dive into Python 3](http://www.diveintopython3.net/)
  - [Python 3 Module of the Week](https://pymotw.com/3/)
  - [Codeacademy](https://www.codecademy.com/)


<a id='ch1'></a>
## Integers, Floats, Strings [^](#ch0)


```python
"Hello World!"
```




    'Hello World!'




```python
type("Hello World!")
```




    str




```python
"Hello" + " " + "World!"
```




    'Hello World!'




```python
type(42)
```




    int




```python
"Benim yaşım" + 42
```


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-5-fa15b47ea3fa> in <module>()
    ----> 1 "Benim yaşım" + 42
    

    TypeError: must be str, not int



```python
-41 + 45*3 + 10**3 + 128//2**7
```




    1095




```python
2009//1000 + 2009%1000 + int("2009".replace("0", ""))
```




    40




```python
kelime = 'beşbin'
print(kelime[0], kelime[3], kelime[-1])
```

    b b n
    


```python
len(kelime)
```




    6




```python
print(list(kelime))
```

    ['b', 'e', 'ş', 'b', 'i', 'n']
    


```python
dir(kelime)
```




    ['__add__',
     '__class__',
     '__contains__',
     '__delattr__',
     '__dir__',
     '__doc__',
     '__eq__',
     '__format__',
     '__ge__',
     '__getattribute__',
     '__getitem__',
     '__getnewargs__',
     '__gt__',
     '__hash__',
     '__init__',
     '__init_subclass__',
     '__iter__',
     '__le__',
     '__len__',
     '__lt__',
     '__mod__',
     '__mul__',
     '__ne__',
     '__new__',
     '__reduce__',
     '__reduce_ex__',
     '__repr__',
     '__rmod__',
     '__rmul__',
     '__setattr__',
     '__sizeof__',
     '__str__',
     '__subclasshook__',
     'capitalize',
     'casefold',
     'center',
     'count',
     'encode',
     'endswith',
     'expandtabs',
     'find',
     'format',
     'format_map',
     'index',
     'isalnum',
     'isalpha',
     'isdecimal',
     'isdigit',
     'isidentifier',
     'islower',
     'isnumeric',
     'isprintable',
     'isspace',
     'istitle',
     'isupper',
     'join',
     'ljust',
     'lower',
     'lstrip',
     'maketrans',
     'partition',
     'replace',
     'rfind',
     'rindex',
     'rjust',
     'rpartition',
     'rsplit',
     'rstrip',
     'split',
     'splitlines',
     'startswith',
     'strip',
     'swapcase',
     'title',
     'translate',
     'upper',
     'zfill']




```python
print(
    kelime.upper(),
    kelime.title(),
    kelime.replace('b', 'f'),
    kelime.split('b'),
    kelime.strip('n'),
    kelime.zfill(15),
    sep='\n'
)
```

    BEŞBIN
    Beşbin
    feşfin
    ['', 'eş', 'in']
    beşbi
    000000000beşbin
    


```python
import ipaddress
import pandas
import datetime

print(type(5/7), 
      type(3), 
      type("üç"), 
      type([1,2,'üç']), 
      type({'gm': 'Cem', 'ykb': 'Mehmet'}), 
      type(('zeki', 'metin')), 
      "",
      type(ipaddress.ip_address('8.8.8.8')), 
      type(pandas.DataFrame([])), 
      type(datetime.date(2016, 11, 12)), sep='\n')
```

    <class 'float'>
    <class 'int'>
    <class 'str'>
    <class 'list'>
    <class 'dict'>
    <class 'tuple'>
    
    <class 'ipaddress.IPv4Address'>
    <class 'pandas.core.frame.DataFrame'>
    <class 'datetime.date'>
    

<a id='ch2'></a>
## Lists, Tuples, Sets, Dictionaries [^](#ch0)

### Listeler


```python
[1,2,3,"kalem","silgi","defter", 3,   4,    5, 'kalem']
```




    [1, 2, 3, 'kalem', 'silgi', 'defter', 3, 4, 5, 'kalem']




```python
dir(list)
```




    ['__add__',
     '__class__',
     '__contains__',
     '__delattr__',
     '__delitem__',
     '__dir__',
     '__doc__',
     '__eq__',
     '__format__',
     '__ge__',
     '__getattribute__',
     '__getitem__',
     '__gt__',
     '__hash__',
     '__iadd__',
     '__imul__',
     '__init__',
     '__init_subclass__',
     '__iter__',
     '__le__',
     '__len__',
     '__lt__',
     '__mul__',
     '__ne__',
     '__new__',
     '__reduce__',
     '__reduce_ex__',
     '__repr__',
     '__reversed__',
     '__rmul__',
     '__setattr__',
     '__setitem__',
     '__sizeof__',
     '__str__',
     '__subclasshook__',
     'append',
     'clear',
     'copy',
     'count',
     'extend',
     'index',
     'insert',
     'pop',
     'remove',
     'reverse',
     'sort']




```python
[method for method in dir(list) if not method.startswith('_')] #List Comprehension
```




    ['append',
     'clear',
     'copy',
     'count',
     'extend',
     'index',
     'insert',
     'pop',
     'remove',
     'reverse',
     'sort']




```python
liste = ['huawei', 'cisco']
liste.append('juniper')
print(liste)
```

    ['huawei', 'cisco', 'juniper']
    


```python
print(liste[0])
print(liste[1])
print(liste[2])
```

    huawei
    cisco
    juniper
    


```python
for cihaz in liste: # liste'deki her bir cihaz için
    print(cihaz) # cihazı yaz
```

    huawei
    cisco
    juniper
    


```python
liste.sort()
print(liste)
```

    ['cisco', 'huawei', 'juniper']
    


```python
liste.reverse()
print(liste)
```

    ['juniper', 'huawei', 'cisco']
    


```python
liste.remove('cisco')
print(liste)
```

    ['juniper', 'huawei']
    

### Tuples


```python
(1,2,3,4, 1) # tuple
```




    (1, 2, 3, 4, 1)




```python
[method for method in dir(tuple) if not method.startswith('_')]
```




    ['count', 'index']




```python
demet = ('python', 'c', 'julia', 'ruby', 'c', 'perl', 'python', 'go', 'c++', 'c')
```


```python
demet.count('c')
```




    3




```python
demet.index('julia')
```




    2




```python
demet[2]
```




    'julia'



### Kümeler


```python
{1,2,3,"kalem","silgi","defter", 3,   4,    5, 'kalem'} #eleman sayısı azaldı
```




    {1, 2, 3, 4, 5, 'silgi', 'kalem', 'defter'}




```python
[method for method in dir(set) if not method.startswith('_')]
```




    ['add',
     'clear',
     'copy',
     'difference',
     'difference_update',
     'discard',
     'intersection',
     'intersection_update',
     'isdisjoint',
     'issubset',
     'issuperset',
     'pop',
     'remove',
     'symmetric_difference',
     'symmetric_difference_update',
     'union',
     'update']




```python
küme = {1,2,3,"kalem","silgi","defter", 3,   4,    5, 'kalem'}
```


```python
küme.add('masa')
print(küme)
print(küme.issuperset({3,4,5}))
print(küme.issubset('kalem'))
```

    {1, 2, 3, 4, 5, 'silgi', 'kalem', 'defter', 'masa'}
    True
    False
    


```python
bazı_doğal_sayılar = {*range(10)}
bazı_asal_sayılar = {2,3,5,7,11,13}
print(bazı_doğal_sayılar)
print(bazı_asal_sayılar)
print(bazı_doğal_sayılar - bazı_asal_sayılar)
print(bazı_asal_sayılar - bazı_doğal_sayılar)
print(bazı_doğal_sayılar.intersection(bazı_asal_sayılar))
print(bazı_asal_sayılar.union(bazı_doğal_sayılar))
```

    {0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
    {2, 3, 5, 7, 11, 13}
    {0, 1, 4, 6, 8, 9}
    {11, 13}
    {2, 3, 5, 7}
    {0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 11, 13}
    

### Sözlükler


```python
{'silgi': 3, 'kalem':2, 'defter': 5}
```




    {'defter': 5, 'kalem': 2, 'silgi': 3}




```python
[method for method in dir(dict) if not method.startswith('_')]
```




    ['clear',
     'copy',
     'fromkeys',
     'get',
     'items',
     'keys',
     'pop',
     'popitem',
     'setdefault',
     'update',
     'values']




```python
sözlük = {
    'router1': {
        'şehir': 'istanbul-avr',
        'marka': 'cisco',
        'model': 'asr9k',
        'ip': '10.0.0.1',
        'version': '15.4rc'
    },
    'router2': {
        'şehir': 'istanbul-and',
        'marka': 'cisco',
        'model': 'asr9k',
        'ip': '10.0.0.2',
        'version': '15.3rc'
    }
}
```


```python
sözlük['router1']
```




    {'ip': '10.0.0.1',
     'marka': 'cisco',
     'model': 'asr9k',
     'version': '15.4rc',
     'şehir': 'istanbul-avr'}




```python
sözlük['router1']['ip']
```




    '10.0.0.1'




```python
print(sözlük.keys())
print(sözlük.values())
```

    dict_keys(['router1', 'router2'])
    dict_values([{'şehir': 'istanbul-avr', 'marka': 'cisco', 'model': 'asr9k', 'ip': '10.0.0.1', 'version': '15.4rc'}, {'şehir': 'istanbul-and', 'marka': 'cisco', 'model': 'asr9k', 'ip': '10.0.0.2', 'version': '15.3rc'}])
    


```python
for key, val in sözlük.items():
    print(key, val['ip'])
```

    router1 10.0.0.1
    router2 10.0.0.2
    

<a id='ch3'></a>
## Fonksiyonlara giriş [^](#ch0)


```python
def f(x):
    return 2*x + 1
```


```python
print(f(4))
print(f(5))
```

    9
    11
    


```python
(f(4) + f(5))/f(2)
```




    4.0




```python
dir(f)
```




    ['__annotations__',
     '__call__',
     '__class__',
     '__closure__',
     '__code__',
     '__defaults__',
     '__delattr__',
     '__dict__',
     '__dir__',
     '__doc__',
     '__eq__',
     '__format__',
     '__ge__',
     '__get__',
     '__getattribute__',
     '__globals__',
     '__gt__',
     '__hash__',
     '__init__',
     '__init_subclass__',
     '__kwdefaults__',
     '__le__',
     '__lt__',
     '__module__',
     '__name__',
     '__ne__',
     '__new__',
     '__qualname__',
     '__reduce__',
     '__reduce_ex__',
     '__repr__',
     '__setattr__',
     '__sizeof__',
     '__str__',
     '__subclasshook__']



<a id='ch4'></a>
## If - elif - else koşulları [^](#ch0)


```python
"Mahmut Tuncer" == "Mahmut" + " "+ "Tuncer"
```




    True




```python
42 > 5*10**2
```




    False




```python
t, f = True, False
t or f
```




    True




```python
print('t: ', t, 'f: ', f)
print('all: ', all([t,f]))
print('any: ', any([t,f]))
```

    t:  True f:  False
    all:  False
    any:  True
    


```python
the_answer = 42
```


```python
bool(the_answer)
```




    True




```python
bool(None or False)
```




    False




```python
#Koşuldan sonra iki nokta, alt satır ve 4 boşluk (veya bir tab) gelir. 
#Python whitespace duyarlı bir dildir.
if t:
    print('koşul sağlandı')
```

    koşul sağlandı
    


```python
if t and f:
    print("Değişkenlerin her ikisi de -boolean- doğru!")
else:
    print("Değişkenlerin en az birisi -boolean- yanlış!")
```

    Değişkenlerin en az birisi -boolean- yanlış!
    


```python
benim_yasim = 29
senin_yasin = int(input('Pardon, yaşınız neydi acaba? '))
if benim_yasim > senin_yasin:
    print('Ben sizden {} yaş daha büyüğüm!'.format(benim_yasim - senin_yasin))
elif benim_yasim < senin_yasin:
    print('Siz benden {} yaş daha büyüksünüz!'.format(senin_yasin - benim_yasim))
else:
    print('Yaşlarımız aynı!')
```

    Pardon, yaşınız neydi acaba? 42
    Siz benden 13 yaş daha büyüksünüz!
    


```python
def fib(n):
    if n < 2:
        return n
    return fib(n-2) + fib(n-1)
```


```python
[fib(i) for i in range(1,15)]
```




    [1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377]



<a id='ch5'></a>
## While, For döngüleri ve iterasyon [^](#ch0)


```python
n=0
while n<5:
    n = n+1
    print(n)
```

    1
    2
    3
    4
    5
    


```python
karakterdizisi = "karakterdizisi"
n = 0
while n < len(karakterdizisi):
    print(karakterdizisi[n])
    n += 1
```

    k
    a
    r
    a
    k
    t
    e
    r
    d
    i
    z
    i
    s
    i
    


```python
"python for network engineers".split()
```




    ['python', 'for', 'network', 'engineers']




```python
sozcukler = "python for network engineers".split()
i = 0
while i < len(sozcukler):
    print(sozcukler[i].title())
    i += 1
```

    Python
    For
    Network
    Engineers
    


```python
for harf in "alfabe":
    if harf not in "aeıioöuü":
        print(harf.upper())
    else:
        print(harf)
```

    a
    L
    F
    a
    B
    e
    

#### İterasyon


```python
iterasyon = iter("abc")
```


```python
next(iterasyon)
```




    'a'




```python
next(iterasyon)
```




    'b'




```python
next(iterasyon)
```




    'c'




```python
next(iterasyon)
```


    ---------------------------------------------------------------------------

    StopIteration                             Traceback (most recent call last)

    <ipython-input-107-70e50cc1b3e7> in <module>()
    ----> 1 next(iterasyon)
    

    StopIteration: 



```python
iterasyon = iter("abc")
for adım in iterasyon:
    print(adım)
```

    a
    b
    c
    


```python
for adım in "abc":
    print(adım)
```

    a
    b
    c
    

## Modüllere Giriş


```python

```
