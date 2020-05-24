---
title: 'AES-GCM Encryption: Javax.crypto vs. Python cryptography'
date: 2019-01-20 00:06:00 +0100
categories: [Crypto, Security]
tags: [crypto, security, android, python]
seo:
  date_modified: 2020-05-24 16:24:55 +0200
---

I am part of a development team that produces an Android application that **exchanges _secrets_ with a server-side application**. These secrets are generated on both client-side and server-side. Before secret data is transfered to the other side, it is protected using a [hybrid cryptosystem](https://en.wikipedia.org/wiki/Hybrid_cryptosystem), where there is a AES-CBC encryption involved.

During the development we bumped into a situation, when we were not able to decrypt the data on the client-side encrypted on the server-side due to slight differenceces in the APIs provided on these two platforms. These are the technologies used:

- **client-side**: Android / Kotlin / [Javax.crypto](https://docs.oracle.com/javase/7/docs/api/javax/crypto/package-summary.html)
- **server-side**: Python 3.6 / [cryptography](https://pypi.org/project/cryptography/)

This post describes the difference and covers the solution.

## Input

**Any binary data** of arbitrary length can be an input to AES encryption. In both of the following examples we will work with the same input data, a UTF-8 encoded string:

```
This is a secret text.
```

This is the base64 representation of the input

```
VGhpcyBpcyBhIHNlY3JldCB0ZXh0Lg==
```

**AES Key** is a binary string of fixed length. For AES-256 encryption a 256 bit key must be used. In the following examples we will use this key (UTF-8 encoded):

> **Don't copy the key**. Always use a secure generator to generate your own random 256 bits!

```
0123456789ABCDEF0123456789ABCDEF
```

Let's jump into the implementation now.

## Android `Javax.crypto` AES encryption

```kotlin
import javax.crypto.Cipher
import javax.crypto.spec.SecretKeySpec

// Input text
val textBytes = "This is a secret text.".toByteArray().

// The key
// Don’t copy the key.
// Always use a secure generator to generate your own random 256 bits!
val keyBytes = "0123456789ABCDEF0123456789ABCDEF".toByteArray()

// Initialize AES cipher
val aesCipher = Cipher.getInstance("AES/GCM/NoPadding")
aesCipher.init(Cipher.ENCRYPT_MODE, SecretKeySpec(keyBytes, "AES"))

val IV = aesCipher.iv

// Output
val encryptedTextBytes = aesCipher.doFinal(textBytes)
```

The `IV` is a twelve-byte init vector generated for us by the `init()` method.

> [Initialization vector (IV)](https://en.wikipedia.org/wiki/Initialization_vector) - a cryptographic primitive used by [block ciphers](https://en.wikipedia.org/wiki/Block_cipher) such as [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).

The init vector will look somewhat like this (Base64):

```
0xjZNe0Mge7cYKyU
```

... and this is the bytes of our encrypted text (Base64):

```
HuhcyjmfByaD2kv1FUfVj1cC3rbitcLmDYJL2Y5o31Zst6k4ZCM=
```

## Python `cryptography` AES encryption

```python
import base64
from cryptography.hazmat.backends import default_backend
from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes

# Input text
text_bytes = "This is a secret text.".encode()

# The key
# Don’t copy the key.
# Always use a secure generator to generate your own random 256 bits!
key_bytes = "0123456789ABCDEF0123456789ABCDEF".encode()

# Init vector
# We will use the same IV generated in the Javax.crypto example
iv = base64.standard_b64decode("0xjZNe0Mge7cYKyU")

# Encrypt the text (AES/GCM/NoPadding)
aes_encryptor = Cipher(
    algorithms.AES(key_bytes),
    modes.GCM(iv),
    backend=default_backend()
).encryptor()

# Output
encrypted_text_bytes = aes_encryptor.update(text_bytes) + aes_encryptor.finalize()
```

And we are done! This is what the base64 representation of the result bytes looks like:

```
HuhcyjmfByaD2kv1FUfVj1cC3rbitQ==
```

## The Difference

Let's compare the two outputs.

**Javax.crypto**

```
HuhcyjmfByaD2kv1FUfVj1cC3rbitcLmDYJL2Y5o31Zst6k4ZCM=
```

**Python cryptography**

```
HuhcyjmfByaD2kv1FUfVj1cC3rbitQ==
```

The first output is obviously larger than the second one. So what are the extra bytes at the end of the first output?

The answer is that **Javax.crypto implementation automatically appends the authentication tag to the final output**.

> In [Galois/Counter Mode (GCM)](https://en.wikipedia.org/wiki/Galois/Counter_Mode) of operation for ciphers like AES, **authentication tag** is used for verification of integrity of the data.

To achieve the same output in Python, following code must be written instead:

```python
encrypted_text_bytes + aes_encryptor.tag
```

## Conclusion

When data is encrypted with AES in CBC mode using the Javax.crypto implementation, the last 16 bytes of the output is the authentication tag.

When data is encrypted with AES in CBC mode in Python, **authentication tag needs to be added to the output manually** so that it can be used later to verify integrity of data during decryption.
