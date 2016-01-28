KeePassPHP
==========

KeePassPHP is a pure-PHP library that can read password databases generated by the password manager KeePass 2.x (.kdbx files). It reads groups, entries and passwords, and makes them easily accessible through a simple PHP API. Its aim is not (yet) to completely replace KeePass: it cannot modify kdbx files, it can just read them.

More generally, KeePassPHP can also encrypt and decrypt files in the same secure way as KeePass does (with integrity checks, brute-force-guessing protection, etc).

Example
-------

See KeePassPHP-UI, a web user interface directly using KeePassPHP to list all entries of a kdbx file, and then extract selected specific passwords on demand.

Library usages
---------------------

KeePassPHP can be used at a high-level, and at a low-level.

* High-level usage: [to be documented]
* Low-level usage: [to be documented]

Internal kphpdb files
---------------------

Decrypting a kdbx file is computationally expensive. Indeed, to prevent an attacker from guessing your main password through brute-force attack, KeePass repeatedly encrypts that password a great number of rounds, to make the whole decryption process artificially hard. Thus, finding the password by brute-force would take a very long time.

But since PHP can be quite slow, decrypting a kdbx file only once can actually take a long time. Moreover, you may have to decrypt a single database twice just to find one password: a first time to get the list of all entries and display them to the user, and a second time to actually get the chosen password (that is typically what happens with KeePassPHP-UI).

To try and fix this, KeePassPHP can extract from kdbx files some of the entries data, without passwords. This extracted, less-sensitive information is then encrypted again and stored alongside the actual kdbx file, but with a lower number of encryption rounds, so that it is cheaper to decrypt. This makes it fast to access the list of entries without passwords; but accessing a password
is still as hard as before. This also makes it possible to store key files associated with kdbx files, when there are some.

Security concerns
------------------

First of all, if you use KeePassPHP on a server that you access through the Internet, you obviously need to secure the communication pipe to that server, since your passwords will be sent back and forth inside that pipe: only allow access to KeePassPHP through https.

Then, since your password database will be stored on that server, it should be hard to break into this server and get the kdbx file itself. Ideally, as hard as it is to do so for your computer, which may actually well be already true.

Another security problem to be aware of is that when you access KeePassPHP on a device which is not yours, you never know how much you can trust this device. It can have a keylogger that could sniff your main password, it can register the passwords you will probably copy in the clipboard, etc. Never use KeePassPHP - or any password manager for that matter - on a computer you cannot trust!

Finally, the idea to store non-sensitive information alongside the real kdbx file, but less expensive to decrypt, can actually destroy the protection against brute-force password guessing. Indeed, if the same password is used to encrypt the kdbx file and the kphpdb file (the KeePassPHP-internal file containing non-sensitive data), it will be as easy to guess it as it is for the kphpdb file, which is by design relatively cheap. If different passwords are used, there is no risk, but the user has to remember another password (that's not the spirit of a password manager). If only one password is used, KeePassPHP uses only half of it to encrypt the kphpdb file, so that the other half of the password is still hard to brute-force-guess.

Note that this last problem regards password guessing; an attacker could still try to decrypt the content of the password database without guessing the password, and the complexity of this task would be unchanged by the kphpdb file.

API
---- 

[to be documented]

Requirements
------------------
This should work with PHP 5.3 and higher, with the common crypto library mcrypt.

License
-------
This work is MIT licensed.
