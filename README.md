# Embedded Linux Systems

![image](https://www.mathworks.com/hardware-support/embedded-linux/_jcr_content/imageParsys/image.adapt.full.medium.png/1620307647773.png)

## Brief

This project involves configuring a bootloader and Linux kernel for an ARM Cortex-A9 processor to run on an embedded Linux system using an SD card. The following steps outline how to set up and run the system:

## Prerequisites

Before proceeding, make sure you have the following packages installed on your system:

- build-essential
- git
- autoconf
- bison
- flex
- texinfo
- help2man
- gawk
- libtool-bin
- libncurses5-dev
- unzip
- libssl-dev
- device-tree-compiler
- swig
- python3-distutils
- python3-dev
- tftpd-hpa
- qemu-system-arm

You can install these packages by running the following command:

```
sudo apt install build-essential git autoconf bison flex texinfo help2man gawk libtool-bin libncurses5-dev unzip libssl-dev device-tree-compiler swig python3-distutils python3-dev tftpd-hpa qemu-system-arm
```

## Linux Image Configuration

1. Copy the two files from the ```Linux image``` directory to the TFTP file directory. The TFTP directory is typically located at ```/srv/tftp```.

## SD Card Setup

1. Navigate to the bootloader directory.

2. Execute the following commands:

   ```
   $ dd if=/dev/zero of=sd.img bs=1M count=1024
   $ cfdisk sd.img
   ```

   In the cfdisk interface, choose the 'dos' option. Create three primary partitions with the following properties:

   - Partition 1: 64MB size, FAT16 partition type. Mark this partition as bootable.
   - Partition 2: 8MB size, Linux type (root filesystem).
   - Partition 3: Use the remaining space, Linux type (data filesystem).

   Press 'Write' to save the partition table.

3. Use the loop driver to emulate block devices from the SD card image and its partitions:

   ```
   $ sudo losetup -f --show --partscan sd.img
   $ sudo mkfs.vfat -F 16 -n boot /dev/loop<x>p1
   $ sudo losetup -d /dev/loop<x>
   ```

## Booting

1. Run the following command to boot the system using QEMU:

   ```
   sudo qemu-system-arm -M vexpress-a9 -m 128M -nographic -kernel u-boot -sd sd.img -net tap,script=./qemu-myifup -net nic -init /bin/sh
   ```

   Press any key when prompted.

## Setting Environment Variables

1. Within the QEMU environment, set the following environment variables:

   ```
   setenv ipaddr 192.168.0.100
   setenv serverip 192.168.0.1
   setenv bootcmd 'tftp 0x61000000 zImage; tftp 0x62000000 vexpress-v2p-ca9.dtb;bootz 0x61000000 - 0x62000000'
   setenv bootargs console=ttyAMA0 root=/dev/nfs ip=192.168.0.100 nfsroot=192.168.0.1:/home/<user>/Documents/GitHub/Embedded-Linux-Systems/nfsroot, nfsvers=3,tcp rw
   saveenv
   ```

2. Reset the system to run QEMU:

   ```
   reset
   ```
The system will now boot successfully ✅.

## Screenshots
<img src="https://github-production-user-asset-6210df.s3.amazonaws.com/25803558/241567635-1d027e8d-73fa-4f38-ab5b-f7b5d92737f1.png" />

## Contributing

Pull requests are welcome. For major changes, please open an [issue](https://github.com/Michael-M-aher/Embeded-Linux-Systems/issues) first to discuss what you would like to change.

Please make sure to update tests as appropriate.

## Authors

👤 **Michael Maher**

- Twitter: [@Michael___Maher](https://twitter.com/Michael___Maher)
- GitHub: [@Michael-M-aher](https://github.com/Michael-M-aher)

Please ⭐️ this repository if this project helped you!

<a href="https://www.buymeacoffee.com/michael.maher" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="Buy Me A Coffee" height="60px" width="200" ></a>

## License

Copyright © 2022 [Michael Maher](https://github.com/Michael-M-aher).
This project is [MIT](https://github.com/Michael-M-aher/Embeded-Linux-Systems/blob/main/LICENSE) licensed.