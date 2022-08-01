# Enable Kali Linux repositories on Debian/Ubuntu

* Import Kali Linux PGP keys from keyserver

```bash
gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys ED444FF07D8D0BF6
```

* Export public key

```bash
gpg --export ED444FF07D8D0BF6 | sudo tee /usr/share/keyrings/kali-archive-keyring >/dev/null
```

* Add Kali Linux repository to apt sources

```bash
cat<<EOF | sudo tee /etc/apt/sources.list.d/kali.list >/dev/null
deb [signed-by=/usr/share/keyrings/kali-archive-keyring] http://http.kali.org/kali kali-rolling main contrib non-free
deb-src [signed-by=/usr/share/keyrings/kali-archive-keyring] http://http.kali.org/kali kali-rolling main non-free contrib
EOF
```

* Update list of available packages

```bash
sudo apt update
```

    #kali #linux #repository
