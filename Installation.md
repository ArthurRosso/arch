#Installation

##Pre-installation: 

###Keyboard
First of all, modify the keyboard to abnt2:

```
# loadkeys br-abnt2
```

###Partition the disk

Using `cfdisk` I did this:

| Partition        | Size             | Name             |
| ---------------- |:----------------:| ----------------:|
| /dev/sda7        | 4M               | Bios boot (grub) |
| /dev/sda8        | 512M             | /boot            |
| /dev/sda9        | 8G               | swap             |
| /dev/sda10       | 30G              | /                |
| /dev/sda11       | Rest of the disk | /home            |

> OBS: I need a partition called BIOS boot because I'm using `GPT`!

###Creating file systems

I chose to use `xfs` as filesystem:

```
# mkfs.xfs /dev/sda8
# mkfs.xfs /dev/sda10
# mkfs.xfs /dev/sda11
```

And `swap` on `/dev/sda9`:

```
# mkswap /dev/sda9
```

> OBS: Please, never touch the BIOS boot!

###Mounting the disks

Mount `/`:
```
# mount /dev/sda10 /mnt
```

Mount `/boot`:
```
# mkdir /mnt/boot
# mount /dev/sda8 /mnt/boot
```

Mount `/home`:
```
# mkdir /mnt/home
# mount /dev/sda11 /mnt/home
```

Mount `swap`:
```
# swapon /dev/sda9
```

> OBS: Never ever touch the BIOS boot!

##Installation

###Select the mirrors

Brazil always :top:
```
# nano /etc/pacman.d/mirrorlist
```

###Install the base packages

Install the packages for a basic system:
```
# pacstrap /mnt base base-devel
```

##Configure the system

###Fstab

I also don't know what it does, but I know it's important:
```
# genfstab -U /mnt >> /mnt/etc/fstab
```

###Chroot

Change the root for edit the system:
```
arch-chroot /mnt
```

###Time zone

Define the time zone:
```
# ln -s /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime
```

Define the hardware clock:
```
# hwclock --systohc
```

###Locale

Uncomment `pt_BR.UTF-8 UTF-8` and run `locale-gen`:
```
# nano /etc/locale.gen
------------------------
pt_BR.UTF-8 UTF-8
------------------------
# locale-gen
```

Set the LANG variable in locale.conf:
```
# nano /etc/locale.conf
------------------------
LANG=pt_BR.UTF-8
```

###Hostname

Create the hostname file:
```
# nano /etc/hostname
------------------------
anarchy
```

Add matching entries to hosts:
```
# nano /etc/hosts
------------------------
127.0.0.1	localhost
::1			localhost
```

Root password:
```
# passwd
```

###Boot loader

```
# pacman -S grub
# grub-install --target=i386-pc --recheck /dev/sda
# cp /usr/share/locale/en\@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo
# grub-mkconfig -o /boot/grub/grub.cfg
```

##Reboot
`CTRL+D` and `reboot`