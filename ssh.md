# SSH - Secure Shell

**Last update**: 20201206



### To do:

1. Read Wikipedia entry
2. Read Linux magazine



### Table of Contents

1. [Introduction](#introduction)
2. [Public and private keys](#public-and-private-keys)


### 1. Introduction <a name="introduction"></a>

SSH uses [public-key cryptography](https://en.wikipedia.org/wiki/Public-key_cryptography) to [authenticate](https://en.wikipedia.org/wiki/Authentication) the remote computer and allow it to authenticate the user, if necessary.[[2\]](https://en.wikipedia.org/wiki/SSH_(Secure_Shell)#cite_note-rfc4252-2) There are several ways to use SSH; one is to use automatically generated public-private key pairs to simply encrypt a network connection, and then use [password](https://en.wikipedia.org/wiki/Password) authentication to log on.

Another is to use a manually generated public-private key pair to perform the authentication, allowing users or programs to log in without having to specify a password. In this scenario, anyone can produce a matching pair of different keys (public and private). The public key is placed on all computers that must allow access to the owner of the matching private key (the owner keeps the private key secret). While authentication is based on the private key, the key itself is never transferred through the network during authentication. SSH only verifies whether the same person offering the public key also owns the matching private key. In all versions of SSH it is important to verify unknown [public keys](https://en.wikipedia.org/wiki/Public_key), i.e. [associate the public keys with identities](https://en.wikipedia.org/wiki/Public-key_cryptography#Associating_public_keys_with_identities), before accepting them as valid. Accepting an attacker's public key without validation will authorize an unauthorized attacker as a valid user.

You can start clicking with the mouse over the terminal, but quickly you will realize that this leads you nowhere. Next, you can start typing and pressing 'Enter', but especially if you do it for the first time most likely whatever you have typed in the terminal will produce only the error messages. Still, that is something, as it clearly means that there is some secret/magic language which is trying to respond to, or to interpret, your command input, as soon as you have typed something in the terminal and pressed 'Enter'. 

What is that secret built-in language available in the terminal? This lecture is all about shedding light on its existence...

Loosely speaking, **shell** is the generic name of any program that user employs to type commands in the terminal (a.k.a. text window).  Example **shells**:

* sh 
* bash
* ksh
* csh
* fish

To get the list of **shells** available on your computer, type in the terminal:

```linux
cat /etc/shells 
```






### 2. Public and private keys <a name="public-and-private-keys"></a>





