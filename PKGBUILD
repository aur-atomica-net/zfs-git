# Maintainer: Jason R. McNeil <jason@jasonrm.net>
# Contributor: Jesus Alvarez <jeezusjr at gmail dot com>
# Contributor: Kyle Fuller <inbox at kylefuller dot co dot uk>

_spl_git_version=$(pacman -Q spl-git | awk '{print $2}')
_zfs_utils_git_version=$(pacman -Q zfs-utils-git | awk '{print $2}')
_kernel_version=$(pacman -Ql linux | grep -oE '[0-9]+\.[0-9]+\.[0-9]+-[0-9]+' | head -n1)
_gitname="zfs"

pkgname="zfs-git"
pkgver=0.6.3_r240_g40749aa_3.19.2_1
pkgrel=1
license=('CDDL')
pkgdesc="Kernel modules for the Zettabyte File System."
depends=("spl-git=${_spl_git_version}" "zfs-utils-git=${_zfs_util_git_version}" "linux=${_kernel_version}")
makedepends=("git" "linux-headers=${_kernel_version}")
arch=("i686" "x86_64")
url="http://zfsonlinux.org/"
source=("${_gitname}::git+https://github.com/zfsonlinux/zfs.git")
groups=("archzfs-git")
md5sums=('SKIP')
replaces=("zfs")
provides=("zfs")
conflicts=("zfs" "zfs-lts")
install=zfs.install

pkgver() {
    cd ${srcdir}/${_gitname}
    REPO_VER=$(git describe --long | sed 's/^zfs-//;s/\([^-]*-g\)/r\1/;s/-/./g')
    KERNEL_VER=$(pacman -Ql linux | grep -oE '[0-9]+\.[0-9]+\.[0-9]+-[0-9]+' | head -n1 | sed -r 's/-/_/g')
    echo "${REPO_VER}_${KERNEL_VER}"
}

build() {
    cd ${srcdir}/${_gitname}
    ./autogen.sh

    ./configure --prefix=/usr \
                --sysconfdir=/etc \
                --sbindir=/usr/bin \
                --libdir=/usr/lib \
                --datadir=/usr/share \
                --includedir=/usr/include \
                --with-udevdir=/lib/udev \
                --libexecdir=/usr/lib/zfs \
                --with-config=kernel \
                --with-linux=/usr/lib/modules/${_kernel_version}-ARCH/build

    make
}

package() {
    cd ${srcdir}/${_gitname}
    make DESTDIR="${pkgdir}" install

    cp -r "$pkgdir"/{lib,usr}
    rm -r "$pkgdir"/lib

    sed -i "s+${srcdir}++" ${pkgdir}/usr/src/zfs-*/${_kernel_version}-ARCH/Module.symvers
}
