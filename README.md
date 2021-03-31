# QEMU Guest Kernel

Linux kernel configurations for QEMU/KVM guests, statically linked for direct kernel boot.

## Architectures

* `i686`: x86, i686 w/o PAE.
  * Choose x86-64 kernel if RAM > 3 GiB.
* `x86_64`: x86, generic x86-64.

## How to build

### Native build

```bash
ARCH=<see_below>

wget url://to/linux-5.10.<y>.tar.xz
tar xf linux-5.10.<y>.tar.xz
cp config.$ARCH linux-5.10.<y>/arch/$ARCH/configs/qemu_extra.config
cd linux-5.10.<y>/

make defconfig
make kvm_guest.config
make qemu_extra.config
make -j<N>
```

### Cross build

Install Clang/LLVM toolchain, then

```bash
export ARCH=<see_below>
export CROSS_COMPILE=<see_below>
export LLVM=1

wget url://to/linux-5.10.<y>.tar.xz
tar xf linux-5.10.<y>.tar.xz
cp config.$ARCH linux-5.10.<y>/arch/$ARCH/configs/qemu_extra.config
cd linux-5.10.<y>/

make <see_below>defconfig
make kvm_guest.config
make qemu_extra.config
make -j<N>
```

| Architecture | Kernel `ARCH` | `CROSS_COMPILE` | `defconfig` prefix |
| ------------ | ------------- | --------------- | ------------------ |
| `i686` | `x86` | `i686-linux-gnu-` | `i386` |
| `x86_64` | `x86` | `x86_64-linux-gnu-` | `x86_64` |

## How to use

* QEMU command line:
  ```
  qemu-system-<arch> -kernel /path/to/bzImage -append "root=/dev/vdXY rootfstype=<fstype> rootflags=<mountflags> rw" ...
  ```
* Libvirt XML:
  ```xml
  <domain type="kvm">
    <!-- ... -->
    <os>
      <!-- ... -->
      <kernel>/path/to/bzImage</kernel>
      <cmdline>root=/dev/vdXY rootfstype=[fstype] rootflags=[mountflags] rw</cmdline>
    </os>
  </domain>
  ```
* Virt-manager GUI:
  * Switch to “Details” tab,
  * In “Boot options”, expand “Direct kernel boot” block,
  * Check “Enable direct kernel boot”,
  * Set “Kernel path” and “Kernel args”, “Initrd path” can be safely omitted.

### AppArmor

The Linux kernel use SELinux as security module by default. To use AppArmor instead, set either `lsm=apparmor` or `selinux=0 security=apparmor` in kernel command line args.
