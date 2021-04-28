# QEMU Guest Kernel: Configurations

## Base

The configuration is based on `defconfig` and `kvm_guest.config`, with minimal modification to support commonly used hardware emulated by QEMU.

* disable loadable module and initramfs (the kernel images are managed by host)
* enable xz compression
* enable `/proc/config.gz`
* enable apparmor
  * to use apparmor, set either `lsm=apparmor` or `selinux=0 security=apparmor` in kernel command line
* enable user namespace (for upower)
* [x86_64] enable x32 (for Gentoo guest)
* enable zram (for Fedora guest, zram is enabled by default since 33)
  * zsmalloc
  * lzo, lz4, lz4hc, zstd

## Drivers

* enable virtio-mmio
* enable balloon
* enable virtio-rng
* enable qxl
* enable stdvga
* enable ac97, generic hda
* enable r8139
* enable TUN/TAP

## File systems

* advanced partition type
  * bsdlabel, solaris, windows dynamic (x86 commons)
  * mac (still supported by modern macOS)
* enable all native file systems, with POSIX ACL, security labels, stats, and quota.
  * enable F2FS compression
* enable FUSE
  * enable virtio-fs
* enable OverlayFS
* enable UDF
* enable FAT utf8 by default
* enable ExFAT
* enable kNTFS with write support
* enable common miscs
  * HFS/HFS+
  * SquashFS with XATTR with all available compression algs
  * UFS with write support
  * EROFS
* enable all codepages in NLS
  * UTF-8 normalization

## Incompatable hardware and related features

Some hardware drivers require a firmware blob to work. However, the kernel is statically linked and will not load firmware from filesystem. Thus they do not work and we disable them to save time and space.

* disable wireless and wlan
* disable i915
