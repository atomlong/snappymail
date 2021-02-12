# Maintainer: George Rawlinson <george@rawlinson.net.nz>

pkgname=snappymail
pkgver=2.3.0
pkgrel=1
pkgdesc="modern PHP webmail client"
arch=('any')
license=('AGPL3')
url="https://github.com/the-djmaze/snappymail"
depends=('php-fpm')
makedepends=('nodejs' 'yarn' 'gulp' 'php' 'brotli')
optdepends=('mariadb: storage backend for contacts'
            'php-pgsql: storage backend for contacts'
            'php-sqlite: storage backend for contacts')
source=("$pkgname-$pkgver.tar.gz::$url/archive/v$pkgver.tar.gz"
        "$pkgname.sysusers"
        "$pkgname.tmpfiles")
b2sums=('f5a8e134c239ffc4136828e67dc25fa9ddfd60bdb0e708b2736c7531a27987e6c55d6e56fdceb5af682ff0478f9f169210c27efc503bdf719524000d2fb3fa1a'
        'e020b2d4bc694ca056f5c15b148c69553ab610b5e1789f52543aa65e098f8097a41709b5b0fc22a6a01088a9d3f14d623b1b6e9ae2570acd4f380f429301c003'
        '2536e11622895322cc752c6b651811b2122d3ae60099fe609609d7b45ba1ed00ea729c23f344405078698d161dbf9bcaffabf8eff14b740acdce3c681c513318')

prepare() {
  sed -i "s/\$sCustomDataPath = '';/\$sCustomDataPath = '\/var\/lib\/$pkgname';/" "$pkgname-$pkgver/$pkgname/v/0.0.0/include.php"

  # create folder for build output
  mkdir -p build
}

build() {
  cd "$pkgname-$pkgver"
  yarn install

  # temporary workaround
  # TODO: examine rollup package from AUR as make dependency
  export PATH="$(yarn global bin):$PATH"
  yarn global add rollup

  # build snappymail
  php release.php --aur
  bsdtar -x \
    -C "$srcdir/build" \
    -f "build/dist/releases/webmail/$pkgver/$pkgname-$pkgver.tar.gz"
  find . -type d -exec chmod 755 {} \;
  find . -type f -exec chmod 644 {} \;
}

package() {
  # directories
  install -dm755 "$pkgdir/usr/share/snappymail" \
    "$pkgdir/var/lib/snappymail"

  # application files
  cp -r "$srcdir/build/snappymail" "$pkgdir/usr/share/snappymail"
  install -Dm644 -t "$pkgdir/usr/share/snappymail" \
    "$srcdir/build/index.php" \
    "$srcdir/build/include.php" \
    "$srcdir/build/serviceworker.js" \
    "$srcdir/build/.htaccess"

  # data files
  cp -r "$srcdir/build/data" "$pkgdir/var/lib/snappymail"

  # sysusers
  install -Dm644 "$srcdir/$pkgname.sysusers" "$pkgdir/usr/lib/sysusers.d/$pkgname.conf"
  install -Dm644 "$srcdir/$pkgname.tmpfiles" "$pkgdir/usr/lib/tmpfiles.d/$pkgname.conf"
}
