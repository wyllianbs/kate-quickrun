# Maintainer: Prof. Wyllian <wyllianbs@gmail.com>
pkgname=kate-quickrun
pkgver=1.0
pkgrel=1
pkgdesc="Quick Run plugin for Kate - compile and run the current file with one shortcut"
arch=('x86_64' 'aarch64')
url="https://github.com/wyllianbs/kate-quickrun"
license=('GPL-2.0-or-later')
depends=('ktexteditor' 'ki18n' 'kparts' 'kiconthemes' 'kservice' 'kxmlgui' 'kconfig' 'kcoreaddons' 'qt6-base')
optdepends=('konsole: embedded terminal (KPart) for the Quick Run own terminal')
makedepends=('cmake' 'extra-cmake-modules' 'gettext')
# For a release tarball, point source/sha to it. For a local build, run
# `makepkg -si` from a checkout with the sources present.
source=()
sha256sums=()

build() {
    cmake -B build -S "${startdir}" \
        -DCMAKE_INSTALL_PREFIX=/usr \
        -DCMAKE_BUILD_TYPE=Release
    cmake --build build
}

package() {
    DESTDIR="${pkgdir}" cmake --install build
}
