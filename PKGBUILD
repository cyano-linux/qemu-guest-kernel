# Maintainer: Cyano Hao <c@cyano.cn>

pkgname=qemu-guest-kernel
pkgdesc="Linux kernels for QEMU/KVM guests (direct kernel boot)"
url="https://github.com/guest-kernel/qemu"

_srcmajor=5
_srcminor=10
_srcpatch=23
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
install=archpkg.install

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
            '77116c808ed9478300252f32f10332d3380201ab1e553b06d9a363e60e268c3d'
            'bd023eb255b7678d26503afa15d1a6058266676fe8025ac6ba9e6573e8707884'
            'e9a8240b8a55f5048229ee87dcccb63a461c0f4496c44e836a1bdf5b0aaffeec')

prepare() {
	cd $srcdir/$_srcname
	patch -p1 -i $srcdir/patch-$pkgver
}

export KBUILD_BUILD_HOST=guest-kernel
export KBUILD_BUILD_USER=qemu

# since we are building for “any” architecture, treat all targets as cross build
export LLVM=1

_build() {
	cd $srcdir/$_srcname
	make mrproper

	export CROSS_COMPILE=$_carch-linux-gnu-

	cp $srcdir/config.$_carch .config
	make olddefconfig
	make bzImage

	cp $(make -s image_name) $srcdir/vmlinuz.$_carch
	# copy updated configuration back to $srcdir
	# the dest is a symlink, remove the symlink first not to overwrite the original file
	cp --remove-destination .config $srcdir/config.$_carch
}

build() {
	ARCH=x86 _carch=i686 _build
	ARCH=x86 _carch=x86_64 _build
}

package() {
	cd $srcdir/
	local target=$pkgdir/usr/share/qemu/kernel
	install -Dm644 -t $target/ $srcdir/vmlinuz.{i686,x86_64}
}
