# TFTP-PXE-Boot-Server

> [!IMPORTANT]
> [PR#5](https://github.com/igorpupkinable/TFTP-PXE-Boot-Server/pull/5) dropped support for 32-bit, CentOS, Fedora and loading files via TFTP

This project contains basic files and folder setup needed for a TFTP PXELINUX server.

Network (PXE) boot supports the following live CD or installation distros for BIOS and UEFI **(without Secure Boot yet)** devices.
* CloneZilla Live 64-bit with additional non-free firmware support
* GParted Live 64-bit
* Ubuntu 16.04 (Xenial)

### Usage
1. Set up TFTP server
    * You could use http://ww2.unime.it/flr/tftpserver/ for MacOS
    * Follow [these](http://www.pyrosoft.co.uk/blog/2013/01/13/setting-up-a-pxe-boot-server-on-synology-dsm-4-2-beta/) and [these](https://kb.synology.com/en-us/DSM/tutorial/How_to_implement_PXE_with_Synology_NAS) instructions on Synology NAS
2. Set up HTTP server pointing to _http/_ directory
    * Debug HTTP server as per instructions [below](#Debug-HTTP-server)
3. Check out this project code on TFTP server
    `git clone --depth 1 git@github.com:igorpupkinable/TFTP-PXE-Boot-Server.git .`
4. Ensure TFTP server root points to _tftp/_ project directory
    * Debug TFTP server as per instructions [below](#Debug-TFTP-server)
5. Configure your DHCP to use TFTP server
    * Use DHCP option 66 "next-server" if located on a different IP to DHCP server
    * DHCP should offer the LPXELINUX.0 as the boot filename (DHCP option 67)
    * Follow [these](https://community.synology.com/enu/forum/2/post/124897) instructions on Synology router
6. Edit _tftp/*/pxelinux.cfg/default_ files to add your PXE boot options
7. Download and configure each bootstrap program you require as per instructions. Bootstraps are **not** committed to this repository due to their size.
    * Alpine Linux (TODO)
    * [CloneZilla Live 64-bit](#CloneZilla-Live-64-bit-instructions)
    * [GParted Live 64-bit](#GParted-Live-64-bit-instructions)
    * Ubuntu Desktop (TODO)
    * Ubuntu Minimal (TODO)
    * Ubuntu Server (TODO)

### Debug TFTP server
_tftp_ is the user interface to the Internet TFTP (Trivial File Transfer Protocol), which allows users to transfer files to and from a remote machine. The remote host may be specified on the command line, in which case tftp uses host as the default host for future transfers.
1. Install TFTP client
  ```shell
  user:~$ sudo apt update
  user:~$ sudo apt install tftp
  ```
2. Test connection and file download
  ```shell
  user:~$ tftp 192.168.0.123
  tftp> verbose
  Verbose mode on.
  tftp> get bios/lpxelinux.0
  getting from 192.168.0.123:bios/lpxelinux.0 to lpxelinux.0 [netascii]
  Received 91044 bytes in 0.2 seconds [3641760 bits/sec]
  tftp> quit
  ```

### Debug HTTP server
1. Download a file placed in _images/_ directory
  ```shell
  user:~$ wget http://192.168.0.123/images/example.ext
  ```

### Alpine Linux instructions
TODO

### CloneZilla Live 64-bit instructions
1. Download _amd64_ ISO or ZIP alternative stable release from [here](https://clonezilla.org/downloads/download.php?branch=alternative)
2. Create image version directory in  _http/_ directory, i.e. `http/images/CloneZilla/20230212-kinetic`
3. Extract _vmlinuz_, _initrd.img_ and _filesystem.squashfs_ files from `live/` directory of the downloaded archive
4. Image version directory should look like this
  > * http/images/CloneZilla/20230212-kinetic/filesystem.squashfs
  > * http/images/CloneZilla/20230212-kinetic/initrd.img
  > * http/images/CloneZilla/20230212-kinetic/vmlinuz
5. Make sure CloneZilla Live x64 _KERNEL_, _APPEND initrd_ and _APPEND fetch_ paths and TFTP/HTTP server IP match in _tftp/*/pxelinux.cfg/default_ configuration file for BIOS and UEFI, e.g.
  > LINUX http://192.168.0.123/images/CloneZilla/20230212-kinetic/vmlinuz
  > APPEND initrd=http://192.168.0.123/images/CloneZilla/20230212-kinetic/initrd.img ... fetch=http://192.168.0.123/images/CloneZilla/20230212-kinetic/filesystem.squashfs

More information can be found [here](https://CloneZilla.org/livepxe.php)

### GParted Live 64-bit instructions
1. Download _amd64_ ISO or ZIP stable release from [here](https://gparted.org/download.php)
2. Create image version directory in _http/_ directory, i.e. `http/images/GParted/1.5.0-1-amd64`
3. Extract _vmlinuz_, _initrd.img_ and _filesystem.squashfs_ files from `live/` directory of the downloaded archive
4. Image version directory should look like this
  > * http/images/GParted/1.5.0-1-amd64/filesystem.squashfs
  > * http/images/GParted/1.5.0-1-amd64/initrd.img
  > * http/images/GParted/1.5.0-1-amd64/vmlinuz
5. Make sure GParted Live x64 _KERNEL_, _APPEND initrd_ and _APPEND fetch_ paths and TFTP/HTTP server IP match in _tftp/*/pxelinux.cfg/default_ configuration file, for BIOS and UEFI, e.g.
  > LINUX http://192.168.0.123/images/GParted/1.5.0-1-amd64/vmlinuz
  > APPEND initrd=http://192.168.0.123/images/GParted/1.5.0-1-amd64/initrd.img ... fetch=http://192.168.0.123/images/GParted/1.5.0-1-amd64/filesystem.squashfs

More information can be found [here](https://gparted.org/livepxe.php)

### Ubuntu Desktop instructions
TODO

### Ubuntu Minimal instructions
TODO

### Ubuntu Server instructions
TODO

---
### Further documentation
The Syslinux Project
https://www.syslinux.org/

PXELINUX is a Syslinux derivative for booting from a network server
https://wiki.syslinux.org/wiki/index.php?title=PXELINUX

* Configuration introduction can be found [here](https://wiki.syslinux.org/wiki/index.php?title=Config)
* Advanced menu system configuration documentation can be found [here](https://wiki.syslinux.org/wiki/index.php?title=Menu)

##### How to upgrade Syslinux
1. Clone repository
  `git clone --depth 1 https://github.com/paulmaunders/TFTP-PXE-Boot-Server .`
2. Download new version from https://wiki.syslinux.org/wiki/index.php?title=Download
  `wget https://mirrors.edge.kernel.org/pub/linux/utils/boot/syslinux/Testing/6.04/syslinux-6.04-pre1.zip`
3. Extract modules and dependencies according to https://wiki.syslinux.org/wiki/index.php?title=Library_modules
  > * bios/com32/elflink/ldlinux.c32
  > * bios/com32/libutil/libutil.c32
  > * bios/com32/menu/menu.c32
  > * bios/com32/modules/linux.c32
  > * bios/core/pxelinux.0
4. DO NOT use Syslinux versions 5.10 to 6.02 due to known bug which crashes VM
  * https://www.virtualbox.org/ticket/13048
  * https://bugzilla.syslinux.org/show_bug.cgi?id=54

##### dnsmasq DHCP server
https://dnsmasq.org/docs/dnsmasq-man.html

Run `dnsmasq --help dhcp` to see available DHCP options.
List of standard DHCP options is [here](https://www.iana.org/assignments/bootp-dhcp-parameters/bootp-dhcp-parameters.xml#options).

##### Testing with Oracle VirtualBox
1. Oracle VirtualBox VM requires at least 1024MB of base memory for successful testing.
2. Oracle VirtualBox VM requires Intel PRO/1000 MT Desktop bridged network adapter for successful BIOS PXE testing.
3. Oracle VirtualBox VM requires virtio-net bridged network adapter for successful UEFI PXE testing.
4. Use Microsoft Hyper-V Generation 2 VM for successful UEFI PXE testing.
