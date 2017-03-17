---
layout: post
title:  "tcl expect ile huawei konfigürasyonu almak"
date:   2017-03-17 12:43:06
categories: config
tags:
  - tcl
  - expect
  - Huawei
  - config
image: /assets/article_images/2017-03-17-expect-ile-konfigurasyon/cables_l.jpg
image2: /assets/article_images/2017-03-17-expect-ile-konfigurasyon/cables_m.jpg
published: true
---
Özellikle network cihazlarından çeşitli komut çıktılarını almak veya konfigürasyon değişikliği yapmak gerektiğinde expect akla ilk gelen seçeneklerden birisi. Cihazdan verilen yanıtlara göre çeşitli alt dallara inilebilmesi; yani "interface down ise şöyle yap, yok interface up fakat üzerinden trafik geçmemişse böyle yap" diyebilmenizi sağlayan bir yapıda olması expect'i uzunca bir süre otomatik konfigürasyon için biçilmiş kaftan kıldı. Aşağıda çok noktalı bir müşteriye ait Huawei cihazlarda snmp konfigürasyonu yapmak için kullandığım kodu bulabilirsiniz.

Biraz açıklamam gerekirse ilgili host'a telnet olmayı deniyor. Telnet başarılı olursa username & password bilgilerini giriyor. Ardından prompt bekleyip uygun komutları gönderiyor. Ekrana da yapılan işlemle ilgili çeşitli logları yazıyor.

``` tcl
#!/usr/bin/expect

set timeout 3
set host [lindex $argv 0]
set hostname [lindex $argv 1]
set username "uname"
set password "s3cr3+"
set command1 "sys"
set command2 "acl number 1234"
set command3 "rule 10 permit source 1.2.3.4 0.0.0.255"
set command4 "quit"
set command5 "snmp-agent community read snmpcommunity acl 1234"
set command6 "return"
set command7 "save all"
set OUTPUT "dummy text"
eval spawn telnet $host
expect {
"Connected" {puts "Connected"}
"Connection closed by foreign host"  {set OUTPUT "Telnet busy, try again later"; puts "### OUTPUT for $hostname@$host: $OUTPUT ###"; exit}
"No route to host" {set OUTPUT "Destination host unreachable"; puts "### OUTPUT for $hostname@$host: $OUTPUT ###"; exit}
timeout {set OUTPUT "connection timeout"; puts "### OUTPUT for $hostname@$host: $OUTPUT ###"; exit}
}
expect {
"sername:" {send "$username\r"}
timeout {set OUTPUT "didnt get username prompt unexpected device maybe";  puts "### OUTPUT for $hostname@$host: $OUTPUT ###"; exit}
}
expect {
"assword:" {send "$password\r"}
timeout {set OUTPUT "didnt get password prompt unexpected device maybe";  puts "### OUTPUT for $hostname@$host: $OUTPUT ###"; exit}
}
expect {
">" {send "$command1\r"}
timeout {set OUTPUT "check login credentials"; puts "### OUTPUT for $hostname@$host: $OUTPUT ###"; exit}
}
expect "\]"
send "$command2\r"
expect "\]"
send "$command3\r"
expect "\]"
send "$command4\r"
expect "\]"
send "$command5\r"
expect "\]"
send "$command6\r"
expect ">"
send "$command7\r"
expect "n\]:"
send "y\r"
expect ">"
set OUTPUT "Done."
send "quit\r"
expect eof
puts "### OUTPUT for $hostname@$host: $OUTPUT ###"
```
