# Maintainer: Thibault Suzanne <thi [DOT] suzanne [AT] gmail [DOT] com>

_srcname='ocsigen-bundle'
pkgname="ocaml-${_srcname}"
pkgver='2.3.0'
pkgrel=0
pkgdesc='Clean and safe tools for developing and running client/server Web 2.0 applications with OCaml'
arch=('i686' 'x86_64')
url='http://ocsigen.org/'
license=('LGPL')
depends=('ocaml>=3.12.0' 'ocaml-compiler-libs' 'libev' 'glibc'
  'ocaml-calendar' 'ocaml-cryptokit' 'ocamlnet' 'ocaml-pcre' 'ocaml-react' 'ocaml-ssl' 'ocaml-sqlite3'
  'ocaml-text')
makedepends=('ocaml-findlib')
optdepends=('ocaml-zip' 'ocamlduce' 'ocaml-type-conv')
provides=("ocaml-ocsigenserver=${pkgver}" "ocaml-lwt=${pkgver}" "ocaml-tyxml=${pkgver}"
  "ocaml-eliom=${pkgver}" "ocaml-js_of_ocaml=${pkgver}" "ocaml-deriving-ocsigen=0.3b")

conflicts=("ocaml-ocsigenserver" "ocaml-lwt" "ocaml-tyxml"
  "ocaml-eliom" "ocaml-js_of_ocaml" "ocaml-deriving-ocsigen")

options=('!strip')
source=("http://ocsigen.org/download/${_srcname}-${pkgver}.tar.gz")
md5sums=('dac0db4775286a4f734b3aa0ade1facf')


build() {
  cd "${srcdir}/${_srcname}-${pkgver}"
  # Arch Linux has no www-data, so use another group instead
  # also, disable DBM because OCaml 4.00 dropped it
  env OCSIGEN_USER=http OCSIGEN_GROUP=http ./configure \
    --prefix=/ \
    --bindir=/usr/bin \
    --sbindir=/usr/sbin \
    --libexecdir=/usr/libexec \
    --sysconfdir=/etc \
    --sharedstatedir=/usr/com \
    --localstatedir=/var \
    --libdir=/usr/lib \
    --includedir=/usr/include \
    --oldincludedir=/usr/include \
    --datarootdir=/usr/share \
    --mandir=/usr/share/man/man1 \
    --docdir=/usr/share/doc/ocsigenserver \
    --disable-dbm
  # the configure script is buggy, specify --without-dbm by force-editing
  sed -i 's/OCSIGENSERVER_OPTS := \(.*\)$/OCSIGENSERVER_OPTS := --without-dbm \1/' \
    Makefile.config
  # done, let's build
  make
}


package() {
  cd "$srcdir/${_srcname}-${pkgver}"
  make \
    SITELIB="${pkgdir}/usr/lib/ocaml" \
    BINDIR="${pkgdir}/usr/bin" \
    LDCONF="${pkgdir}/usr/lib/ocaml/ld.conf" \
    COMMANDPIPE="${pkgdir}/var/run/ocsigen" \
    CONFIGDIR="${pkgdir}/etc/ocsigenserver" \
    LOGDIR="${pkgdir}/var/log/ocsigenserver" \
    STATICPAGESDIR="${pkgdir}/srv/ocsigenserver" \
    DATADIR="${pkgdir}/var/lib/ocsigenserver" \
    MANDIR="${pkgdir}/usr/share/man/man1" \
    -e install

  rm -f "${pkgdir}/usr/lib/ocaml/ld.conf"

  # Fixing miswritten directories in ocsigenserver configuration file
  sed -i -e "s#//#/#g; s#/var/www/#/srv/#g" "${pkgdir}/etc/ocsigenserver/ocsigenserver.conf"

 # Writing lines for eliom support by default in ocsigenserver configuration file
  sed -i -e '26 s#<!--##; 31 s#-->#<extension findlib-package="eliom.server"/>#' \
    "${pkgdir}/etc/ocsigenserver/ocsigenserver.conf"
}
