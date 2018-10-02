# Maintainer: Chris Hobbs (RX14) <chris@rx14.co.uk>
# Contributor: Sven-Hendrik Haase <sh@lutzhaase.com>
# Contributor: SanskritFritz (gmail)

pkgname=netdata-git
_gitname=netdata
pkgver=v1.10.0.r752.g07060aec
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
            '6a7b6294f84abadd23d69e0325887464bcf31c03910414c2d110be4c8fb53e9e487893654d4183b9ccd106b83a0cefd374098019b604bcf20ae87dec3d4c2a9e'
            '03aab5d4a6e22f165e69fad595c1e73b4d5fccd7b01aac3af63d75e3a80f5263f19fcc6bac4921fc762e90ea36b40afc0bb956668944f2ccbcd744b8f6f65972'
            'fa54d1936d93858cc6b9e30f0a47578dfdafdc7c6528f43ab74a6974e52df1c678f0846babacf4e996d7356c966fd3e285db01fe305a61a9dd3a2b6e3239106e')


pkgver() {
  cd "$_gitname"
  git describe --long | sed 's/\([^-]*-g\)/r\1/;s/-/./g'
}

prepare() {
  pwd
  patch -Np0 <cgroup-name-machinectl.patch
  patch -Np0 <hddtemp_use_id_for_name.patch
  patch -Np0 <name_disks_by_vdev.patch
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

