# Maintainer: xsigil
pkgname=street-facs-fighter-git
pkgver=r10.9f52c36
pkgrel=1
pkgdesc="A terminal-based street fighter game for FACS (Facial Action Coding System)"
arch=('x86_64' 'aarch64')
url="https://github.com/xsigil/street-facs-fighter"
license=('custom')
depends=('glibc')
makedepends=('go' 'git')
provides=('street-facs-fighter')
conflicts=('street-facs-fighter')
source=("git+https://github.com/xsigil/street-facs-fighter.git")
sha256sums=('SKIP')

pkgver() {
  cd "${srcdir}/street-facs-fighter"
  printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short=7 HEAD)"
}

build() {
  cd "${srcdir}/street-facs-fighter"
  export CGO_CPPFLAGS="${CPPFLAGS}"
  export CGO_CFLAGS="${CFLAGS}"
  export CGO_CXXFLAGS="${CXXFLAGS}"
  export CGO_LDFLAGS="${LDFLAGS}"
  export GOFLAGS="-buildmode=pie -trimpath -ldflags=-linkmode=external -mod=readonly -modcacherw"

  go build -o bin/street-facs-fighter ./cmd/street-facs-fighter
  if [ -d "./cmd/sff-importer" ]; then
    go build -o bin/sff-importer ./cmd/sff-importer
  fi
}

package() {
  cd "${srcdir}/street-facs-fighter"

  install -dm755 "${pkgdir}/usr/share/street-facs-fighter"
  install -dm755 "${pkgdir}/usr/bin"

  cp -a assets "${pkgdir}/usr/share/street-facs-fighter/"
  [ -f app.db ] && install -Dm644 app.db "${pkgdir}/usr/share/street-facs-fighter/app.db"
  [ -f facs_master_dataset.tsv ] && install -Dm644 facs_master_dataset.tsv "${pkgdir}/usr/share/street-facs-fighter/facs_master_dataset.tsv"

  install -Dm755 bin/street-facs-fighter "${pkgdir}/usr/share/street-facs-fighter/street-facs-fighter"
  if [ -f bin/sff-importer ]; then
    install -Dm755 bin/sff-importer "${pkgdir}/usr/share/street-facs-fighter/sff-importer"
  fi

  cat << 'WRAPPER' > "${pkgdir}/usr/bin/street-facs-fighter"
#!/usr/bin/env sh
cd /usr/share/street-facs-fighter || exit 1
exec ./street-facs-fighter "$@"
WRAPPER
  chmod 755 "${pkgdir}/usr/bin/street-facs-fighter"

  if [ -f LICENSE ]; then
    install -Dm644 LICENSE "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
  fi
}
