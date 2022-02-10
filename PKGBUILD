# Maintainer: Filma Bem <filmabemtv2@gmail.com>
pkgname=calamares
pkgver=3.2.51
_pkgver=3.2.51
pkgrel=1
_commit=
pkgdesc='Distribution-independent installer framework'
arch=('i686' 'x86_64')
license=(GPL)
url="https://gitlab.manjaro.org/applications/calamares"
license=('LGPL')
depends=('kconfig' 'kcoreaddons' 'kiconthemes' 'ki18n' 'kio' 'solid' 'yaml-cpp' 'kpmcore>=4.2.0' 'mkinitcpio-openswap'
         'boost-libs' 'ckbcomp' 'hwinfo' 'qt5-svg' 'polkit-qt5' 'gtk-update-icon-cache' 'plasma-framework'
         'qt5-xmlpatterns' 'squashfs-tools' 'libpwquality' 'appstream-qt' 'icu')
makedepends=('extra-cmake-modules' 'qt5-tools' 'qt5-translations' 'git' 'boost')
backup=('usr/share/calamares/modules/bootloader.conf'
        'usr/share/calamares/modules/displaymanager.conf'
        'usr/share/calamares/modules/initcpio.conf'
        'usr/share/calamares/modules/unpackfs.conf')

source+=("$pkgname-$pkgver-$pkgrel.tar.gz::$url/-/archive/v$pkgver/calamares-v$pkgver.tar.gz"
		 "fbpos-calamares.zip"
         #"$pkgname-$pkgver-$pkgrel.tar.gz::$url/-/archive/$_commit/$pkgname-$_commit.tar.gz"
        )
sha256sums=('56c46f3ab2be52b27530fb08a53a9b1337f735c9473e1ecb5da49930e70f8774'
            '11df76de914141342d2827a79491b998471c473cb57edafeea3287916e745b7f')

prepare() {
	#mv ${srcdir}/calamares-${_commit} ${srcdir}/calamares-${pkgver}
	#mv ${srcdir}/calamares-v${pkgver} ${srcdir}/calamares-${pkgver}
	cd ${srcdir}/calamares-v${pkgver}
	sed -i -e 's/"Install configuration files" OFF/"Install configuration files" ON/' CMakeLists.txt
	
	# change version
	sed -i -e "s|$pkgver|$_pkgver|g" CMakeLists.txt
	#_ver="$(cat CMakeLists.txt | grep -m3 -e "  VERSION" | grep -o "[[:digit:]]*" | xargs | sed s'/ /./g')"
	_ver="$pkgver"
	printf 'Version: %s-%s' "${_ver}" "${pkgrel}"
	sed -i -e "s|\${CALAMARES_VERSION_MAJOR}.\${CALAMARES_VERSION_MINOR}.\${CALAMARES_VERSION_PATCH}|${_ver}-${pkgrel}|g" CMakeLists.txt
	sed -i -e "s|CALAMARES_VERSION_RC 1|CALAMARES_VERSION_RC 0|g" CMakeLists.txt

	# change branding
	sed -i -e "s/default/manjaro/g" src/branding/CMakeLists.txt
}

build() {
	cd ${srcdir}/calamares-v${pkgver}

	mkdir -p build
	cd build
        cmake .. \
              -DCMAKE_BUILD_TYPE=Release \
              -DCMAKE_INSTALL_PREFIX=/usr \
              -DCMAKE_INSTALL_LIBDIR=lib \
              -DWITH_KF5DBus=OFF \
              -DBoost_NO_BOOST_CMAKE=ON \
              -DSKIP_MODULES="tracking interactiveterminal initramfs \
                              initramfscfg dracut dracutlukscfg \
                              dummyprocess dummypython dummycpp \
                              dummypythonqt services-openrc \
                              keyboardq localeq welcomeq"
        make
}

package() {
	cd ${srcdir}/calamares-v${pkgver}/build
	make DESTDIR="$pkgdir" install
	install -Dm644 "../data/manjaro-icon.svg" "$pkgdir/usr/share/icons/hicolor/scalable/apps/calamares.svg"
	install -Dm644 "../data/calamares.desktop" "$pkgdir/usr/share/applications/calamares.desktop"
	install -Dm755 "../data/calamares_polkit" "$pkgdir/usr/bin/calamares_polkit"
	install -Dm644 "../data/49-nopasswd-calamares.rules" "$pkgdir/etc/polkit-1/rules.d/49-nopasswd-calamares.rules"
	chmod 750      "$pkgdir"/etc/polkit-1/rules.d

	# rename services-systemd back to services
	mv "$pkgdir/usr/lib/calamares/modules/services-systemd" "$pkgdir/usr/lib/calamares/modules/services"
	mv "$pkgdir/usr/share/calamares/modules/services-systemd.conf" "$pkgdir/usr/share/calamares/modules/services.conf"
	sed -i -e 's/-systemd//' "$pkgdir/usr/lib/calamares/modules/services/module.desc"
	sed -i -e 's/-systemd//' "$pkgdir/usr/share/calamares/settings.conf"
	mkdir "${pkgdir}/etc/calamares"
	mkdir "${pkgdir}/etc/calamares/branding"
	mkdir "${pkgdir}/etc/calamares/branding/fbpos"
	cp -R "${srcdir}/settings.conf" "${pkgdir}/etc/calamares/"
	cp -R "${srcdir}/settings.conf" "${pkgdir}/usr/share/calamares/"
	cp -R "${srcdir}/fbpos/branding.desc" "${pkgdir}/etc/calamares/branding/fbpos/"
	cp -R "${srcdir}/fbpos/show.qml" "${pkgdir}/etc/calamares/branding/fbpos/"
	cp -R "${srcdir}/fbpos/squid.png" "${pkgdir}/etc/calamares/branding/fbpos/"
	cp -R "${srcdir}/fbpos/stylesheet.qss" "${pkgdir}/etc/calamares/branding/fbpos/"
	cp -R "${srcdir}/calamares.desktop" "${pkgdir}/usr/share/applications/"
	cp -R "${srcdir}/netinstall.conf" "${pkgdir}/usr/share/calamares/modules/"
	cp -R "${srcdir}/welcome.conf" "${pkgdir}/usr/share/calamares/modules/"
}
