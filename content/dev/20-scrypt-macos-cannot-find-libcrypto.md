---
title: "python scrypt cannot find libcrypto on macos"
date: 2020-04-07T11:43:40+07:00
draft: false
showDate: true
tags: ["dev", "python", "scrypt", "openssl", "macos"]
---

Today, I tried starting our local django development environment but it failed because of this error.

```
OSError: dlopen(lib/python2.7/site-packages/_scrypt.so, 6): Library not loaded: /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib
```

What does this error mean?

The precompiled scrypt is targeting a now non exising `libcrypto.1.0.0.dylib`, when I look at the location, it's been replaced by an updated api version number `libcrypto.1.1.dylib`.

A quick solution is to soft link it to the exact same path as previous and hope that the api didn't change much.

```sh
ln -s /usr/local/opt/openssl/lib/libcrypto.1.1.dylib /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib
```

That works for me, it might not for you.

If it didn't, another solution, which is probably a better one, is to recompile scrypt with that updated version of openssl.

Instruction is on the [scrypt repo](https://bitbucket.org/mhallin/py-scrypt/src/default/).
