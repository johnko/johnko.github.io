---
title: Convert SSL pfSense Key to RSA Key
date: 2014-10-21
tags: [ ssl, openssl ]
---

> I don't use this anymore, but kept the instructions here for future reference

Source: [http://stackoverflow.com/questions/17733536/how-do-i-convert-a-private-key-to-an-rsa-private-key][1]

[1]: http://stackoverflow.com/questions/17733536/how-do-i-convert-a-private-key-to-an-rsa-private-key

I had to convert some SSL certificates and keys that pfSense created so they could be used on FreeNAS:

```sh
openssl rsa -in server.key -out server_rsa.key
```

I could then combine the block of:

```
-----BEGIN RSA PRIVATE KEY-----
...
-----END RSA PRIVATE KEY-----
-----BEGIN CERTIFICATE-----
...
-----END CERTIFICATE-----
```

And paste them in to FreeNAS'

- Settings
  - SSL
    - SSL Certificate
