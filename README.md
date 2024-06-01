# Pop! OS in Surface Studio

## Steps from linux-surface Repository

First you need to import the keys we use to sign packages.

```
wget -qO - https://raw.githubusercontent.com/linux-surface/linux-surface/master/pkg/keys/surface.asc \
    | gpg --dearmor | sudo dd of=/etc/apt/trusted.gpg.d/linux-surface.gpg
```

After this you can add the repository configuration and update APT.

```
echo "deb [arch=amd64] https://pkg.surfacelinux.com/debian release main" \
	| sudo tee /etc/apt/sources.list.d/linux-surface.list
```

```
sudo apt update
```

Now you can install the linux-surface kernel and its dependencies.

```
sudo apt install linux-image-surface linux-headers-surface libwacom-surface iptsd
```

Then, reboot.

## Custom Steps

### Dual boot with Windows

Open `/boot/efi/loader/loader.conf`

```
sudo gedit /boot/efi/loader/loader.conf
```

Add these lines at the end. Increase the boot wait time for systemd. This will make the systemd boot loader wait for 10 seconds before automatically booting into Pop!_OS.

```
timeout 10
entries 1
```

Then, install the `os-prober` utility which will automatically fetch all the installed operating systems in the machine. Open your terminal and run the following command.

```
sudo apt update
sudo apt install os-prober -y
```

Run the `os-prober` command and it will show you the windows boot manager entry,

```
sudo os-prober
```
![image](https://github.com/tareqmahmood/popos-surface-studio/assets/14259907/50debda1-f5ce-4afd-bdec-dfc6308ea4cd)

Note the partition, for example, here it is `/dev/sda1`. In my case, it was `/dev/nvme0n1p1`. Mount that partition and check contents:

```
sudo mount /dev/nvme0n1p1 /mnt
ls -l /mnt/EFI
```

Now copy the Microsoft bootloader to systemd bootloader:

```
sudo cp -ax /mnt/EFI/Microsoft /boot/efi/EFI
```

### Dual boot time fix

```
sudo timedatectl set-local-rtc 1
sudo hwclock --systohc --localtime
```

### NVIDIA

Check `nvidia-smi`. If not working, re-install nvidia drivers.

```
sudo apt purge ~nnvidia
sudo apt install system76-driver-nvidia
```

Then reboot.

### Touchpad

TBD
