---
layout: post
title:  "Scp ile fortigate backup almak"
date:   2017-03-09 22:42:25
categories: python
tags:
  - python
  - fortigate
  - scp
  - config
  - backup
image: /assets/article_images/2017-03-09-fortigate-backups/bankvault.jpg
image2: /assets/article_images/2017-03-09-fortigate-backups/bankvault_mob.jpg
---

Fortigate'in 5.2.x sürümünde automatic backup özelliği mevcut değil. [Burada][networktools] yer alan kodu temel alan aşağıdaki script'i kullanabilirsiniz.

```python
from fortibackup import getconf

fortigates = {
	'FW1': '1.1.1.1',
	'FW2': '2.2.2.2',
	'FW3': '3.3.3.3',
	'FW4': '4.4.4.4'
}

for fw_name, ipaddress in fortigates.items():
	try:
		getconf(ip = ipaddress, fname = fw_name, uname = 'username', pword = 'password')
		print(f'Got {fw_name} backup config.')
	except:
		print(f'Couldn\'t get {fw_name}\'s backup config from {ipaddress}')
```
[networktools]: https://github.com/gunhanoral/networktools/blob/master/fortibackup.py
