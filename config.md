# QEMU Guest Kernel: Configurations

## Base

The configuration is based on `defconfig` and `kvm_guest.config`, with minimal modification to support commonly used hardware emulated by QEMU.

* disable loadable module and initramfs (the kernel images are managed by host)
* enable `/proc/config.gz`
* [x86_64] enable x32 (for Gentoo guest)
* enable zram (for Fedora guest, zram is enabled by default since 33)
  * zsmalloc
  * lzo, lz4, lz4hc, zstd

## Drivers

* enable virtio-mmio
* enable balloon
* enable virtio-rng
* enable qxl
* enable ac97, generic hda
* enable r8139

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
