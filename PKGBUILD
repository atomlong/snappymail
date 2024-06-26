# Maintainer: George Rawlinson <george@rawlinson.net.nz>

pkgname=snappymail
pkgver=2.36.1
pkgrel=1
pkgdesc='A simple, modern & fast web-based email client'
arch=('any')
license=('AGPL-3.0-or-later')
url='https://snappymail.eu'
depends=('php-fpm' 'php-sodium')
makedepends=(
  'git'
  'nodejs'
  'yarn'
  'gulp'
  'php'
  'brotli'
)
optdepends=(
  'mariadb: storage backend for contacts'
  'php-pgsql: storage backend for contacts'
  'php-sqlite: storage backend for contacts'
)
source=(
  "$pkgname::git+https://github.com/the-djmaze/snappymail.git#tag=v$pkgver"
  'sysusers.conf'
  'tmpfiles.conf'
)
sha512sums=('04ad79629233c07be51b3aafa1e76b5924de6ff2ef52efdc0d97d8c5c125ae7e078a946ed62c99c29405dc4b8a7a7aab7bef4fa8ea3595d7e89f157f494a2679'
            '5dc55dc3655bd2e075dfc69572cde967d4ec9b5ebe27807a893c1beaa681e1af8639a80e3c09d665ddb7a6f40475ae6ad951ca9a243d1533ad68783077a1d51a'
            '991dfdb415f2cff30107addf6fcf9349865a7d91a8e2f2c42b3d561541016f1af41f41aeec23300de8b3d9b75938f14dd073fd6daa245b5fcbcb9ca2166adb48')
b2sums=('138b4a9733141ca8d5add3870be41a174ddc6c485cdc5e9bfc36e5855a79825c327e232676d8f08613faae382aac287ec9fc1a2a9c5b584022891081e4aa21dc'
        'e020b2d4bc694ca056f5c15b148c69553ab610b5e1789f52543aa65e098f8097a41709b5b0fc22a6a01088a9d3f14d623b1b6e9ae2570acd4f380f429301c003'
        'be25b05d775ac4e88065e8018a1026b4c41a073639e59e6520818b7ca578cc93ac2120bd090caf1d255fade69a6680ca764b14cb32f6dd61c80b521d1b16f205')

prepare() {
  # create folder for build output
  mkdir -p build

  cd "$pkgname"

  # ensure data path is set to /var/lib/snappymail
  sed \
    -i snappymail/v/0.0.0/include.php \
    -e "s/\$sCustomDataPath = '';/\$sCustomDataPath = '\/var\/lib\/$pkgname';/" 

  # remove unnecessary $PATH variables
  sed \
    -i snappymail/v/0.0.0/app/libraries/snappymail/gpg/base.php \
    -e '/\(\/sw\/bin\|local\/bin\)/d'

}

build() {
  cd "$pkgname"

  yarn install

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
  install -vdm755 \
    "$pkgdir/usr/share/snappymail" \
    "$pkgdir/var/lib/snappymail"

  # application files
  cp -rv "$srcdir/build/snappymail" "$pkgdir/usr/share/snappymail"
  install -vDm644 -t "$pkgdir/usr/share/snappymail" \
    "$srcdir/build/index.php" \
    "$srcdir/build/include.php" \
    "$srcdir/build/.htaccess"

  # data files
  cp -rv "$srcdir/build/data" "$pkgdir/var/lib/snappymail"

  # systemd configuration
  install -vDm644 sysusers.conf "$pkgdir/usr/lib/sysusers.d/$pkgname.conf"
  install -vDm644 tmpfiles.conf "$pkgdir/usr/lib/tmpfiles.d/$pkgname.conf"
}
