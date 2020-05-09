---
title: "AES Encryption: Javax.crypto vs. Python cryptography"
date: 2019-01-20 00:06:00 +0100
categories: [Crypto, Security]
tags: [crypto, aes, security, android, python]
--- 

We are integrating a third party card payment terminal to our Android application. In order to make a payment terminal work with a device, it needs to be initialized with **secret credentials**. These credentials can be set up:
1. **On the device**. They get persisted on the device too so that the payment terminal SDK can initialize on another run time.
2. **On the server**. The credentials are synchronized to the device later.

To meet a confidentiality requirement, each device uses Android Keystore to generate a 2048 bit RSA key pair. The public key is submitted to the server side. On both, device and server sides, it is used to encrypt generated AES key, which is then used for encryption of the secret credentials (every time credentials are encrypted, a different AES key is generated).

This post covers a step _before_ RSA encryption - the implementation of the **AES encryption** on the **server-side (Python)** and **client-side (Android)** and highlights a slight **difference** we observed.
 
## Implementation

Languages the implementation will be described for are **Kotlin** - the language of choice on the device side, and **Python** - the server-side technology in our project.

**Input** to the snippets of code below is any binary `data`. In the following examples, let's work with input data of a fixed lenght. For the example let's work with this string:

```
This is a secret text.
```

**Output** is a binary blob that looks like this:

![](/assets/img/posts/2020-02-20-aes-encryption-in-android-and-python/1.png)

Such data structure can be then transfered to another party that can decrypt it if it posesses the same AES key.

**AES Key** (UTF-8 decoded)

> **Don't copy the key**. Always use a secure random generator to generate your own 256 bits!

```
0123456789ABCDEF0123456789ABCDEF
```


### Client Side Encryption (Android Javax.crypto)


```
// Inut text
val textBytes = "This is a secret text.".toByteArray().

// The key
val keyBytes = "0123456789ABCDEF0123456789ABCDEF".toByteArray()

// Initialize AES cipher
val aesCipher = Cipher.getInstance("AES/GCM/NoPadding")
aesCipher.init(Cipher.ENCRYPT_MODE, SecretKeySpec(keyBytes, "AES"))

val IV = aesCipher.iv
```

The `IV` is an [initialization vector (IV)](https://en.wikipedia.org/wiki/Initialization_vector) - a cryptographic primitive used by [block ciphers](https://en.wikipedia.org/wiki/Block_cipher) such as [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard). It is generated for us by the `init()` method.

```
// Encrypt text
val encryptedTextBytes = aesCipher.doFinal(textBytes)
```

**Initialization vector** is generated for u


Encrypt data

```
// Encrypt plaintext by AES secret key
val aesCipher = Cipher.getInstance("AES/GCM/NoPadding")
aesCipher.init(Cipher.ENCRYPT_MODE, secretKey)
val encryptedText = aesCipher.doFinal(plaintext)
```


```
val cipher = Cipher.getInstance("RSA/ECB/PKCS1Padding")
cipher.init(KeyProperties.PURPOSE_ENCRYPT, keyPair.public)
val encryptedKey = cipher.doFinal(secretKey.encoded)
```
