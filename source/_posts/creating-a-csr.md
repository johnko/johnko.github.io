---
title: Creating a CSR
date: 2014-10-25
tags: [ ssl, openssl, csr ]
---

Source: [http://www.rackspace.com/knowledge_center/article/generate-a-csr-with-openssl][1]

[1]: http://www.rackspace.com/knowledge_center/article/generate-a-csr-with-openssl


I created a private key:

```sh
openssl genrsa -out ~/domain.com.ssl/domain.com.key 2048
```

Then created the CSR and followed the prompts:

You may want to try:

```sh
openssl req -new -sha256 -key ~/domain.com.ssl/domain.com.key -out ~/domain.com.ssl/domain.com.csr
```

Otherwise:

```sh
openssl req -new -key ~/domain.com.ssl/domain.com.key -out ~/domain.com.ssl/domain.com.csr
```

Then I skipped the key generating on my Certificate Authority provider's page, and pasted the CSR contents and continued.

I then got back a certificate I could use.
