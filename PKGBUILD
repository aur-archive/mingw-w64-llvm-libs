pkgname=mingw-w64-llvm-libs
pkgver=3.5.1
pkgrel=2
pkgdesc="Low Level Virtual Machine (runtime library) (mingw-w64)"
arch=(any)
url="http://llvm.org"
license=("custom:University of Illinois/NCSA Open Source License")
makedepends=(mingw-w64-cmake "llvm>=$pkgver" python)
depends=(mingw-w64-libffi mingw-w64-libxml2)
options=(staticlibs !strip !buildflags !emptydirs)
source=("http://llvm.org/releases/$pkgver/llvm-$pkgver.src.tar.xz"{,.sig})
sha256sums=('bf3275d2d7890015c8d8f5e6f4f882f8cf3bf51967297ebe74111d6d8b53be15'
'SKIP')
validpgpkeys=('54E3BDE33185D9F69664D22455F5CD70BB5A0569'
              '11E521D646982372EB577A1F8F0871F202119294')

_architectures="i686-w64-mingw32 x86_64-w64-mingw32"

build() {
	for _arch in ${_architectures}; do
		unset LDFLAGS
		ffiver=`${_arch}-pkg-config --modversion libffi`
		if [ ${_arch} = "x86_64-w64-mingw32" ]; then
			CPPFLAGS+="-D__USING_SJLJ_EXCEPTIONS__"
		fi
		export PYTHON_EXECUTABLE=/usr/bin/python
		mkdir -p "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
		cd "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
		${_arch}-cmake ../llvm-$pkgver.src \
			-DBUILD_SHARED_LIBS=OFF \
			-DCPACK_BINARY_NSIS=OFF \
			-DCPACK_SOURCE_ZIP=OFF \
			-DFFI_INCLUDE_DIR=/usr/${_arch}/lib/libffi-$ffiver/include \
			-DFFI_LIBRARY_DIR=/usr/${_arch}/lib \
			-DLLVM_BUILD_TOOLS=OFF \
			-DLLVM_ENABLE_FFI=ON \
			-DLLVM_INCLUDE_DOCS=OFF \
			-DLLVM_INCLUDE_EXAMPLES=OFF \
			-DLLVM_INCLUDE_TESTS=OFF \
			-DLLVM_INCLUDE_TOOLS=OFF \
			-DLLVM_TABLEGEN=/usr/bin/llvm-tblgen
		make REQUIRES_RTTI=1
	done
}

package() {
	mkdir -p "$pkgdir/usr/bin"
	for _arch in ${_architectures}; do
		cd "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
		make DESTDIR="$pkgdir" install
		find "$pkgdir/usr/${_arch}" -name '*.exe' -o -name '*gtest*' -o -name 'llvm-lit' | xargs -rtl1 rm
		find "$pkgdir/usr/${_arch}" -name '*.dll' | xargs -rtl1 ${_arch}-strip --strip-unneeded
		find "$pkgdir/usr/${_arch}" -name '*.a' -o -name '*.dll' | xargs -rtl1 ${_arch}-strip -g
		rm -r "$pkgdir/usr/${_arch}/share"
	done
}
