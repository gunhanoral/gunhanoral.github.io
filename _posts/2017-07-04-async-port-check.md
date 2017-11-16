---
layout: post
title:  "Python Asyncio port scanner"
description: "Today I needed to scan a few thousands of network devices on some ports and of course I needed some kind of concurrency."
date:   2017-07-04 19:14:41
categories: python
tags:
  - python
  - tcp
  - port check
  - port scanner
  - asyncio
  - featured
image: /assets/article_images/2017-07-04-async-port-check/asyncio.png
published: true
---
Today I needed to scan a few thousands of network devices on some ports and of course I needed some kind of concurrency. And here below is a quick, dirty and mostly crappy implementation of asyncio port scanning. Use at your own risk.

``` python

import asyncio
import time

now = time.time()

async def check_port(ip, port, loop):
        conn = asyncio.open_connection(ip, port, loop=loop)
        try:
                reader, writer = await asyncio.wait_for(conn, timeout=3)
                print(ip, port, 'ok')
                return (ip, port, True)
        except:
                print(ip, port, 'nok')
                return (ip, port, False)
        finally:
                if 'writer' in locals():
                        writer.close()

async def run(dests, ports, loop):
        tasks = [asyncio.ensure_future(check_port(d, p, loop)) for d in dests for p in ports]
        responses = await asyncio.gather(*tasks)
        return responses

dests = ['asgdshdsgagşilkı', '1.1.1.2', '1.1.1.3']
ports = [22, 23]

loop = asyncio.get_event_loop()
future = asyncio.ensure_future(run(dests, ports, loop))
loop.run_until_complete(future)
print('#'*50)
print('Results: ', future.result())
print('#'*50)
print('Total time: ', time.time() - now)

```

And the output:

```
> python test.py
asgdshdsgagşilkı 22 nok
asgdshdsgagşilkı 23 nok
1.1.1.2 22 nok
1.1.1.3 23 nok
1.1.1.2 23 nok
1.1.1.3 22 nok
##################################################
Results:  [('asgdshdsgagşilkı', 22, False), ('asgdshdsgagşilkı', 23, False), ('1.1.1.2', 22, False), ('1.1.1.2', 23, False), ('1.1.1.3', 22, False), ('1.1.1.3', 23, False)]
##################################################
Total time:  21.0092453956604
```

A quick note: If you run this script on a Windows machine for lots of destination address, you'll most likely get a ValueError like this. "ValueError: too many file descriptors in select()" Since I have a Linux machine, I didn't bother searching a solution.

Update: The solution for this problem is using `asyncio.Semaphore`. It limits the maximum concurrent connection. [Source](https://pawelmhm.github.io/asyncio/python/aiohttp/2016/04/22/asyncio-aiohttp.html).

``` python
async def check_port(ip, port, loop):
        conn = asyncio.open_connection(ip, port, loop=loop)
        try:
                reader, writer = await asyncio.wait_for(conn, timeout=3)
                print(ip, port, 'ok')
                return (ip, port, True)
        except:
                print(ip, port, 'nok')
                return (ip, port, False)
        finally:
                if 'writer' in locals():
                        writer.close()

async def check_port_sem(sem, ip, port, loop):
        async with sem:
                return await check_port(ip, port, loop)

async def run(dests, ports, loop):
        sem = asyncio.Semaphore(400) #Change this value for limitation
        tasks = [asyncio.ensure_future(check_port_sem(sem, d, p, loop)) for d in dests for p in ports]
        responses = await asyncio.gather(*tasks)
        return responses
```
