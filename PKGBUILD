# Maintainer: Chris Hobbs (RX14) <chris@rx14.co.uk>
# Contributor: Sven-Hendrik Haase <sh@lutzhaase.com>
# Contributor: SanskritFritz (gmail)

pkgname=netdata-git
_gitname=netdata
pkgver=v1.11.0.r41.g7d617b0f
pkgrel=1
pkgdesc="Real-time performance monitoring, in the greatest possible detail, over the web"
url="https://github.com/firehol/netdata/wiki"
arch=('x86_64')
license=('GPL')
depends=('libmnl' 'libnetfilter_acct' 'zlib')
optdepends=('nodejs: for monitoring named and SNMP devices'
            'lm_sensors: for monitoring hardware sensors'
            'iproute2: for monitoring Linux QoS'
            'python: for most of the external plugins'
            'python-psycopg2: for monitoring PostgreSQL databases'
            'python-requests: for monitoring elasticsearch'
            'hddtemp: for monitoring hhd temperature'
            'apcupsd: for monitoring APC UPS'
            'iw: for monitoring Linux as access point')
source=("$_gitname::git+https://github.com/firehol/netdata"
        "${_gitname}.tmpfiles"
        "${_gitname}.sysusers"
        'cgroup-name-machinectl.patch'
        'hddtemp_use_id_for_name.patch'
        'name_disks_by_vdev.patch')

provides=('netdata')
conflicts=('netdata')
install="$_gitname.install"
sha512sums=('SKIP'
            '3f934ddd1f5248f9e11c29050c023d60b76e4098ec9c8d413bb362d43e9242f767fd58310d966076e8400779af8bda2459afcc314b267fcb9f1c84173e14e313'
            'a910809a823ba58ca7bdaa72e8d68427b48f452c1fbb2343fa182ecb0a091a7640e73af24b8ba2fdd90e00aed8ef53b7fccd25cb8f04ca9b9fa6c8e52223ca66'
            '1bda6f3b1f68ba2b0ff176acaefbac2b091776b722585c73c9317ba5b400f363f52dc92e94529a52ba0378d1601a7ea1a6dd26704401f9b473cec1da9b60a470'
            '6e7ffb9b2a04e39155bb329c0a46d10a0aca586b4d624bb18320e2d815009e548211ad7f1f8bb484d67fb84a0f062d1045c9207399ce7e261bb9baa99d32964a'
            '68c598b0564bab639f28617a994da4c67701f53172f7ba7b8b8e2ec183cc6de9c2faef380228cbadf33360012cb52d8f85c2eb4fd6b3c3400dbe9347cab87c1f')


pkgver() {
  cd "$_gitname"
  git describe --long | sed 's/\([^-]*-g\)/r\1/;s/-/./g'
}

prepare() {
  pwd
  patch -d netdata -Np1 <cgroup-name-machinectl.patch
  patch -d netdata -Np1 <hddtemp_use_id_for_name.patch
  patch -d netdata -Np1 <name_disks_by_vdev.patch
}

build() {
  cd "$_gitname"

  autoreconf -ivf

  ./configure \
    --prefix="/usr" \
    --sbindir="/usr/bin" \
    --sysconfdir="/etc" \
    --libexecdir="/usr/lib" \
    --localstatedir="/var" \
    --with-zlib --with-math --with-user=netdata
  make
}

package() {
  cd "$_gitname"

  make DESTDIR="$pkgdir" install

  # Remove /var/*, pacman creates it via tmpfiles hook
  rm -vrf "${pkgdir}/var"

  mkdir -p "$pkgdir/etc/netdata"
  chown -R 134:134 "$pkgdir"/etc/netdata
  chown -R 134:134 "$pkgdir"/usr/share/netdata/web

  install -Dm0644 "system/netdata.service" "$pkgdir/usr/lib/systemd/system/netdata.service"
  install -Dm0644 "system/netdata.logrotate" "$pkgdir/etc/logrotate.d/netdata"
  install -Dm0644 "${srcdir}/${_gitname}.tmpfiles" "${pkgdir}/usr/lib/tmpfiles.d/${_gitname}.conf"
  install -Dm0644 "${srcdir}/${_gitname}.sysusers" "${pkgdir}/usr/lib/sysusers.d/${_gitname}.conf"
}

