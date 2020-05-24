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
 
Languages the implementation will be described for are **Kotlin** - the language of choice on the device side, and **Python** - the server-side technology in our project.

## Inputs

**Binary data** of arbitrary length can be input to an AES encryption. In the following examples we will work with input data of a fixed lenght. In the examples we will be encrypting a  UTF-8 encoded string:

```
This is a secret text.
```

This is the base64 representation of the input

```
VGhpcyBpcyBhIHNlY3JldCB0ZXh0Lg==
```

**AES Key** is a binary string of fixed length. For AES-256 encryption a 256 bit key must be used. In the following examples we will use this key (UTF-8 decoded):

> **Don't copy the key**. Always use a secure generator to generate your own random 256 bits!

```
0123456789ABCDEF0123456789ABCDEF
```

Let's jump into the implementation now.

## Android `Javax.crypto` AES encryption

```
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

// Encrypt text
val encryptedTextBytes = aesCipher.doFinal(textBytes)
```

The `IV` is a twelve-byte init vector is generated for us by the `init()` method.

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

```
from cryptography.hazmat.backends import default_backend
from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes

# Input text
text_bytes = "This is a secret text.".encode("utf-8")

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
    modes.GCM(
        iv
    ),
    backend=default_backend()
).encryptor()

encrypted_text_bytes = aes_encryptor.update(text_bytes) + aes_encryptor.finalize()

```

And we are done! This is what the base64 representation of the result bytes looks like:

```
HuhcyjmfByaD2kv1FUfVj1cC3rbitQ==
```

## The difference

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

