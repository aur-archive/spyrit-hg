# Maintainer: Clément Mairet <clement.mairet@tigris.fr>
pkgname=spyrit-hg
pkgver=590
pkgrel=1
pkgdesc="A modern client for MUSH, MUCK, MOO and MUD textual roleplaying games, development version."
arch=("any")
url="http://spyrit.ierne.eu.org/"
license=("GPL")
depends=("python2-pyqt" "mercurial")
optdepends=("python-pygame: for sound notifications")
conflicts=("spyrit")
source=('001-connect_line-setting.patch'
        'spyrit.desktop')
md5sums=('558fcf17f80d1448aee1862587536af4'
         '89cece671ebd94a53d67af9cfe344624')

_hgroot="https://bitbucket.org/balinares"
_hgrepo="spyrit"

build() {
  cd "$srcdir"
  msg "Connecting to Mercurial server...."

  if [ -d $_hgrepo ] ; then
    cd $_hgrepo
    hg pull -u >/dev/null 2>&1 || return 1
    msg "The local files are updated."
  else
    hg clone $_hgroot/$_hgrepo $_hgrepo || return 1
  fi

  msg "Mercurial checkout done or server timeout"
  msg "Starting build..."

  rm -rf "$srcdir/$_hgrepo-build"
  cp -r "$srcdir/$_hgrepo" "$srcdir/$_hgrepo-build"
  cd "$srcdir/$_hgrepo-build"

  #
  # BUILD HERE
  #

  msg2 "Applying connect_line patch..."
  # Apply connect_line patch to allow sending autoconnect text
  patch -uNp1 -i ../001-connect_line-setting.patch >/dev/null 2>&1 || return 1

  # Generate resources
  msg2 "Generating resources..."
  python2 src/resources/spyrcc.py src/resources/resources.qrc -o src/resources.py

  # Build standalone script
  msg2 "Bundling standalone script..."
  python2 dist/build.py src/spyrit.py > ${pkgname}-${pkgver}.py
  sed -i "1s/python/python2/" ${pkgname}-${pkgver}.py
}

package() {
  install -D -m755 $srcdir/$_hgrepo-build/${pkgname}-${pkgver}.py $pkgdir/usr/bin/$_hgrepo

  # Install desktop-related files
  install -D -m644 $srcdir/spyrit.desktop ${pkgdir}/usr/share/applications/spyrit.desktop
  install -D -m644 $srcdir/$_hgrepo-build/src/resources/spyrit-icon.png ${pkgdir}/usr/share/pixmaps/spyrit-icon.png
}
