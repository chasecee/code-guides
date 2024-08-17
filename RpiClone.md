**Steps to Clone Raspberry Pi OS to a USB Drive and Boot from It**

1. **Identify the USB Drive** :

- Plug in the USB drive and run:

```bash
lsblk
```

- Note the device name for the USB drive (e.g., `/dev/sda`).

2. **Prepare the USB Drive** :

```bash
sudo umount /dev/sda1 /dev/sda2
sudo parted /dev/sda --script mklabel msdos
sudo parted /dev/sda --script mkpart primary fat32 0% 512MB
sudo parted /dev/sda --script mkpart primary ext4 512MB 100%
sudo mkfs.vfat /dev/sda1
sudo mkfs.ext4 /dev/sda2
```

3. **Clone the SD Card to the USB Drive** :

```bash
sudo rpi-clone /dev/sda
```

4. **Update `cmdline.txt` on the USB Drive** :

```bash
sudo mount /dev/sda1 /mnt/usb
sudo nano /mnt/usb/cmdline.txt
# Change the root PARTUUID to match the USB drive's root partition (e.g., root=PARTUUID=b02043ed-02)
sudo umount /mnt/usb
```

5. **Unmount All USB Partitions** :

```bash
sudo umount /dev/sda1 /dev/sda2
```

6. **Reboot and Boot from the USB Drive** :

- Shut down the Raspberry Pi:

```bash
sudo shutdown -h now
```

- Remove the SD card, plug in the USB drive, and power on the Raspberry Pi.
