# Basics of GNUPG

## Get `gpg`

* On Debian/Ubuntu (or Windows Subsystem for Linux)

```bash
sudo apt install gnupg
```

* On OSX

```bash
brew install gnupg
```

## Generating PGP keys

```
gpg --full-gen-key
```

* Choose the default key algorithm (RSA)
* Key size should be >= 3072 (I recommend going with 4096 bits - the
  largest available currently).
* Set expiry to `0` (for now) i.e. the key never expires.
* Enter your real name and real email address. Anybody who has access to
  your public key can look up these information.

## Listing keys

* List public keys with,

```bash
gpg -k
# or
gpg --list-key
```

* List secret/private keys with,

```bash
gpg --list-secret-keys
```

## Export public key

```bash
gpg --export -a -o pub.asc
```

* The `-a` stands for "armoured output". Always use the `-a` flag when
  the key is meant to be shared, otherwise I'll hunt you down for
  messing up my terminal buffer :)
* Unarmoured key typically ends with `.gpg`, whereas armoured ones end
  with `.asc`.

## Export private key

* Never share your private key with anybody, except with me (of course)
  @[murtaza@murtazau.xyz](mailto:murtaza@murtazau.xyz).

```bash
gpg --export-secret-keys -a -o priv.asc
```

## Import public key

```bash
gpg --import someones_public_key.asc

gpg: key DB171D028FE3E194: public key "Murtaza Udaipurwala <murtaza@murtazau.xyz>" imported
gpg: Total number processed: 1
gpg:               imported: 1
```

## Encrypt a message

Encrypting a super secret message for `myfriend@gmail.com`

* At sender's end

```bash
gpg --encrypt --recipient 'myfriend@gmail.com' secret.txt
# or
gpg -e -r 'myfriend@gmail.com' secret.txt
```

This encrypts `secret.txt` using receiver's **public** key.

* On receiver's end

```bash
gpg --decrypt secret.txt.gpg
# or
gpg -d secret.txt.gpg
```

Receiver uses his/her **private** key to decrypt the message.

## Signing

When a message is signed,

* One can verify the data was created by a certain sender
* The sender cannot deny sending the data

In order to make sure the encrypted message (here, `secret.txt.gpg`) is
coming from certain individual, the encrypted message is signed with the
sender's private key (yes private key and not public key). Now anybody
in the world can verify who the sender of this message is, provided they
know the sender's public key.

At sender's end,

```bash
gpg --sign secret.txt.gpg
# or
gpg -s secret.txt.gpg
```

At receiver's end,

```bash
# verifying signature
gpg --verify secret.txt.gpg.gpg

# decrypting using sender's public key
gpg --decrypt secret.txt.gpg.gpg

# decrypting using receiver's private key
gpg --decrypt secret.txt.gpg
```

    #cryptography
