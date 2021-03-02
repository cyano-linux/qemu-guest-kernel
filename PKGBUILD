# Maintainer: Cyano Hao <c@cyano.cn>

pkgname=qemu-guest-kernel
pkgdesc="The Linux kernel for QEMU/KVM guests (direct kernel boot)"
url="https://github.com/guest-kernel/qemu"

_srcmajor=5
_srcminor=10
_srcpatch=19
_srcbase=$_srcmajor.$_srcminor
_srcname=linux-$_srcbase

pkgver=$_srcbase.$_srcpatch
pkgrel=1
arch=(any)
license=(GPL2)
makedepends=(
	bc kmod libelf pahole cpio perl tar xz
	llvm clang
)
options=('!strip')
source=(
	"https://cdn.kernel.org/pub/linux/kernel/v$_srcmajor.x/linux-$_srcbase".tar.{xz,sign}
	"https://cdn.kernel.org/pub/linux/kernel/v$_srcmajor.x/patch-$pkgver".xz
	config.{i686,x86_64}
)
validpgpkeys=(
	"ABAF11C65A2970B130ABE3C479BE3E4300411886"  # Linus Torvalds
	"647F28654894E3BD457199BE38DBBDC86092693E"  # Greg Kroah-Hartman
)
sha256sums=('dcdf99e43e98330d925016985bfbc7b83c66d367b714b2de0cbbfcbf83d8ca43'
            'SKIP'
            'b9214b48dd1ae9dd9a6420469212df209105fe04ce6930d063cb987f283528d3'
            '6d15f08876062a5954a7fcbf1a05626d0f65ad7d6e4a1d5e367241b77edeaf0d'
            'b63d1ae870ac820b25c23b4c412962eca18a10fb39d36a164eb0c84b7f6b7b93')

prepare() {
	cd $srcdir/$_srcname
	patch -p1 -i $srcdir/patch-$pkgver
}

export KBUILD_BUILD_HOST=guest-kernel
export KBUILD_BUILD_USER=qemu

# since we are building for “any” architecture, treat all targets as cross build
export LLVM=1

_build_i686() {
	cd $srcdir/$_srcname
	make mrproper

	export ARCH=x86
	export CROSS_COMPILE=i686-linux-gnu-

	cp $srcdir/config.i686 .config
	make olddefconfig
	make bzImage

	cp arch/x86/boot/bzImage $srcdir/vmlinuz.i686
}

_build_x86_64() {
	cd $srcdir/$_srcname
	make mrproper

	export ARCH=x86
	export CROSS_COMPILE=x86_64-linux-gnu-

	cp $srcdir/config.x86_64 .config
	make olddefconfig
	make bzImage

	cp arch/x86/boot/bzImage $srcdir/vmlinuz.x86_64
}

build() {
	_build_i686
	_build_x86_64
}

package() {
	cd $srcdir/
	local target=$pkgdir/usr/share/qemu/kernel
	install -Dm644 -t $target/ $srcdir/vmlinuz.{i686,x86_64}
}
