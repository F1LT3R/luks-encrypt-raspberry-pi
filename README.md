# LUKS Encrypt Raspberry PI

## What You Will Need

1. Raspberry PI
2. SDCard w/ Raspberry PI OS Lite installed
3. Flash drive connected to the RPI (to copy data from root partition during encrypt)
4. Bash scripts: https://github.com/F1LT3R/luks-encrypt-raspberry-pi/tree-save/main/README.md

## Install OS and Update Kernel

1. Burn the Raspberry PI OS to the SDCard w/ `Balenar Etcher` or `Raspberry PI Imager`

2. Copy install scripts into `/boot/install/`

3. Boot into the Raspberry PI and run `sudo /boot/install/1.update.sh`

4. `sudo reboot`  to load the updated kernel


## Install Enc Tools and Prep `initramfs`

1. Run script `/boot/install/2.disk_encrypt.sh`

2. `sudo reboot` to drop into the initramfs shell. 


## Mount and Encrypt

1. Mount master block device to `/tmp/boot/`

    ```shell
    mkdir /tmp/boot
    mount /dev/mmcblk0p1 /tmp/boot/
    ```

2. Run the encryption script, passing your flash drive descriptor:

    ```shell
    /tmp/boot/install/3.disk_encrypt_initramfs.sh [sda|sdb|etc] 
    ```

3. When LUKS encrypts the root partition it will ask you to type `YES` (in uppercase).

4. Create a decryption password (you will be asked twice).

5. LUKS will ask for the decryption password again to copy the data back from the flash drive to the root partition.

6. `reboot -f` to drop back into initramfs.


## Unlock and Reboot to OS

1. Mount master block device at `/tmp/boot/`

    ```shell
    mkdir /tmp/boot
    mount /dev/mmcblk0p1 /tmp/boot/
    ```

2. Open the LUKS encrypted disk:

    ```shell
    /tmp/boot/install/4.luks_open.sh
    ```
  
3. Type in your decryption password again.

4. `exit` to quit BusyBox and boot normally.


## Rebuild `initramfs` for Normal Boot


1. Run script: `/boot/install/5.rebuild_initram.sh`


2. `sudo reboot` into Raspberry PI OS.

3. You should be asked for your decryption password every time you boot.

    ```shell
    Please unlock disc sdcard: _
    ```
____

## References

- Source: https://forums.raspberrypi.com/viewtopic.php?t=219867
- https://github.com/johnshearing/MyEtherWalletOffline/blob/master/Air-Gap_Setup.md#setup-luks-full-disk-encryption
- https://robpol86.com/raspberry_pi_luks.html
- https://www.howtoforge.com/automatically-unlock-luks-encrypted-drives-with-a-keyfile

