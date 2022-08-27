# Install software from ISO via apt

* Mount ISO file at `/media/cdrom` as as `iso9660` file system

```bash
sudo mount -t iso9660 -o loop PATH/TO/ISO /media/cdrom
```

* Configure apt source list to point to `/media/cdrom`

```bash
# /etc/apt/sources.list

deb [trusted=yes] file:/media/cdrom bullseye main non-free contrib
```

* Update repositories

```bash
sudo apt update
sudo apt install <software>
```

    #apt #iso #debian #ubuntu #linux
