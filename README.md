# Ubuntu Preseed ISO Generator

Generate an ISO image for automated Ubuntu 20.04/Focal or 22.04/Jammy desktop installations. This script uses the traditional preseed method.

### Behavior

Check out the usage information below for arguments. The basic idea is to take an unmodified Ubuntu ISO image, extract it, add some kernel command line parameters and a preseed file, then repack the data into a new ISO. Creating the preseed file itself is outside the scope of this tool.

There is an example preseed file ```example.seed``` in this repository which will install Ubuntu using US English settings and UTC time zone with a user named "User" and password "ubuntu". You could modify that file to create your own custom configuration. Unlike the server version of this script, there is currently no way to provide the preseed configuration on a separate volume during the installation - it must be baked into the ISO image.

This script can use an existing ISO image or download the latest daily 64-bit image from the Ubuntu project. Using a fresh ISO speeds things up because there won't be as many packages to update during the installation.

By default, the source ISO image is checked for integrity and authenticity using GPG. This can be disabled with ```-k```.

### Requirements

Tested on a host running Ubuntu 18.04 and 20.04 LTS. Works from within WSL on Windows.

- Utilities required:
    - ```p7zip-full```
    - ```mkisofs``` or ```genisoimage```
    - ```isolinux```

```shell
sudo apt-get install -y p7zip-full mkisofs genisoimage xorriso isolinux syslinux-common
```

### Usage

```
Usage: ubuntu-preseed-iso-generator.sh [-h] [-k] [-v] [-p preseed-configuration-file] [-s source-iso-file] [-d destination-iso-file]

This script will create fully-automated Ubuntu Desktop installation media.

Available options:

-h, --help          Print this help and exit
-v, --verbose       Print script debug info
-p, --preseed       Path to preseed configuration file.
-k, --no-verify     Disable GPG verification of the source ISO file. By default SHA256SUMS-<current date> and
                    SHA256SUMS-<current date>.gpg in <script directory> will be used to verify the authenticity and integrity
                    of the source ISO file. If they are not present the latest daily SHA256SUMS will be
                    downloaded and saved in <script directory>. The Ubuntu signing key will be downloaded and
                    saved in a new keyring in <script directory>
-s, --source        Source ISO file. By default the latest daily ISO for Ubuntu 20.04 will be downloaded
                    and saved as <script directory>/ubuntu-original-<current date>.iso
                    That file will be used by default if it already exists.
-d, --destination   Destination ISO file. By default <script directory>/ubuntu-preseed-<current date>.iso will be
                    created, overwriting any existing file.
```

### Example

```shell
 bash ubuntu-preseed-iso-generator.sh -p example.seed -d ubuntu-desk-20-auto.iso
```

Now you can boot your target machine using ```ubuntu-desk-20-auto.iso``` and it will automatically install Ubuntu using the configuration from ```example.seed```.

#### copying

To copy your ISO to a USB key:

```shell
# find the device
lsblk
lsusb
sudo dmesg
# unmount the usb dev with ONE of these two commands:
sudo umount /media/user/data
sudo umount /dev/sdb
# copy iso to USB
sudo dd if=~/ubuntu-auto.iso of=/dev/sdb bs=1M status=progress && sync
```

Remove the USB key and place into new machine to install on.

### Thanks

This script is based on [this](https://betterdev.blog/minimal-safe-bash-script-template/) minimal safe bash template, and steps found in [this](https://askubuntu.com/questions/806820/how-do-i-create-a-completely-unattended-install-of-ubuntu-desktop-16-04-1-lts) Ask Ubuntu answer.

Fork from: https://github.com/covertsh/ubuntu-preseed-iso-generator

### License

MIT license.
