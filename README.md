15_netinstall: Install Arch/Debian/FreeBSD directly from an internet mirror
===========================================================================

Installation
------------

1. Requires a Linux system using the GRUB2 bootloader.
2. Download 15_netinstall to `/etc/grub.d/15_netinstall`.
3. `chmod +x /etc/grub.d/15_netinstall`
4. `update-grub`
   This will download the iPXE loader to `/boot`, detect your IPv4 network
   config and add the netinstall menu entries to your `grub.cfg`.
5. Reboot.

Additional steps required
-------------------------

This script was primarily written for (re-)installing virtual private servers,
the hosters of which usually don't run a DHCP server.
One therefore needs to manually configure the network — first for iPXE, and
again for the actual installation.
The script automatically adds the necessary configuration for iPXE to the GRUB
commandline, but some of the installers require manual work:

### Arch Linux

The Arch installer will boot with proper network interface configuration, but
without a DNS resolver configured. Let it attempt to download `airootfs.sfs`,
which will timeout after 20 seconds.
Then `echo 'nameserver 8.8.8.8' > /etc/resolv.conf` and download `airootfs.sfs`
to the directory that was created by the installer — should be something under
`/run` (both the directory and the URL will still be visible on your screen, so
you can copy it from there). Exit the shell and the system should proceed to
boot the installer.

### Debian

The Debian installer asks you for your network configuration if it cannot
automatically detect it, so you should be all set just following the installer.

### FreeBSD

Set up the network, download the `MANIFEST` that contains the checksums of the
installation packages, start the installer:

    ifconfig xn0 inet <public ip> netmask 255.255.255.0
    route delete default; route add default <public ip x.x.x.1>
    echo "nameserver 8.8.8.8" > /etc/resolv.conf
    mkdir /usr/freebsd-dist
    cd /usr/freebsd-dist
    fetch http://ftp.freebsd.org/FreeBSD/releases/amd64/amd64/11.0-RELEASE/MANIFEST
    bsdinstall

