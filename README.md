# QEMU Guest Kernel

Linux kernel configurations for QEMU/KVM guest, statically linked for direct kernel boot.

## Architectures

* `i686`: x86, i686 w/o PAE.
  * Choose x86-64 kernel if RAM > 3 GiB.
* `x86_64`: x86, generic x86-64.

## How to build

### Native build

```bash
wget url://to/linux-5.10.<y>.tar.xz
tar xf linux-5.10.<y>.tar.xz
cp config.<arch> linux-5.10.<y>/.config
cd linux-5.10.<y>/
make -j<N>
```

### Cross build

Install LLVM/Clang toolchain, then

```bash
wget url://to/linux-5.10.<y>.tar.xz
tar xf linux-5.10.<y>.tar.xz
cp config.<arch> linux-5.10.<y>/.config
cd linux-5.10.<y>/

export ARCH=<see_below>
export CROSS_COMPILE=<see_below>
export LLVM=1
make -j<N>
```

| config arch | kernel `ARCH` | `CROSS_COMPILE` |
| ----------- | ------------- | --------------- |
| `i686` | `x86` or `i386` | `i686-linux-gnu-` |
| `x86_64` | `x86` or `x86_64` | `x86_64-linux-gnu-` |

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
