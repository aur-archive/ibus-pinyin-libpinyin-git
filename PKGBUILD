# Maintainer:  Yangtse Su<yangtsesu@gmail.com>
# Contributor:  Jekyll Wu<adaptee [at] gmail [dot] com>
# Contributor: riverscn<riverscn at gmail.com>
# Contributor: rainy<rainylau at gmail.com>
# Contributor: Lee.MaRS<leemars at gmail.com>

pkgname=ibus-pinyin-libpinyin-git
pkgver=20120529
pkgrel=1
pkgdesc="The PinYin Engine for IBus Input Frameworki With libpinyin"
arch=('i686' 'x86_64')
license=('LGPL')
url="http://ibus.googlecode.com"
depends=('ibus>=1.3' 'libsigc++2.0' 'python2' 'libpinyin')
makedepends=('git' 'gnome-common' 'intltool')
provides=('ibus-pinyin')
conflicts=('ibus-pinyin' 'ibus-pinyin-git')
install=ibus-pinyin.install
source=(http://ibus.googlecode.com/files/pinyin-database-1.2.99.tar.bz2)
noextract=(pinyin-database-1.2.99.tar.bz2)
md5sums=('d0951b8daa7f56a2cbd3b6b4e42532e0')

_gitname="ibus-pinyin"
_gitroot="https://github.com/epico/ibus-pinyin.git"

build() {
  cd ${srcdir}

  msg "Connecting to ${_gitname} GIT server..."
  if [ -d ${_gitname} ]; then
      cd ${_gitname} && git pull origin
      msg "The local files are updated."
  else
      git clone ${_gitroot}
  fi

  msg "GIT checkout done or server timeout. Preparing sources..."
  rm -rf "${srcdir}/${_gitname}-build"
  cp -r "${srcdir}/${_gitname}" "${srcdir}/${_gitname}-build"
  cd "${srcdir}/${_gitname}-build"
  git checkout -b integration origin/integration
  git reset --hard 7a2b74326e
  ln -s "${srcdir}/pinyin-database-1.2.99.tar.bz2" "${srcdir}/${_gitname}-build/data/db/open-phrase"

  msg "Starting make..."
  cd "${srcdir}/${_gitname}-build"

  # python2 fix
  for file in $(find . -name '*.py' -print); do
    sed -i 's_^#!.*/usr/bin/python_#!/usr/bin/python2_' $file
    sed -i 's_^#!.*/usr/bin/env.*python_#!/usr/bin/env python2_' $file
  done

  for file in setup/ibus-setup-pinyin.in; do
    sed -i 's_exec python_exec python2_' $file
  done

  ./autogen.sh --break-configure || :
  ./configure \
    PYTHON=/usr/bin/python2 \
    --prefix=/usr \
    --libexecdir=/usr/lib/ibus \
    --disable-boost \
    --enable-db-open-phrase || return 1

  make || return 1
}

package() {
  cd "${srcdir}/${_gitname}-build"
  make NO_INDEX=true DESTDIR=${pkgdir} install || return 1
}
