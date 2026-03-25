# SPDX-License-Identifier: AGPL-3.0

#    ----------------------------------------------------------------------
#    Copyright © 2024, 2025, 2026  Pellegrino Prevete
#
#    All rights reserved
#    ----------------------------------------------------------------------
#
#    This program is free software: you can redistribute it and/or modify
#    it under the terms of the GNU Affero General Public License as
#    published by the Free Software Foundation, either version 3 of
#    the License, or (at your option) any later version.
#
#    This program is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU Affero General Public License for more details.
#
#    You should have received a copy of the GNU Affero General Public License
#    along with this program.
#    If not, see <https://www.gnu.org/licenses/>.

# Maintainers:
#   Truocolo
#     <truocolo@aol.com>
#     <truocolo@0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b>
#   Pellegrino Prevete (dvorak)
#     <pellegrinoprevete@gmail.com>
#     <dvorak@0x87003Bd6C074C713783df04f36517451fF34CBEf>
#   Jesus Alonso
#     <doragasu at hotmail dot com>

# NOTE: As I want these packages for Genesis/Megadrive development, they do
# only support the m68000 CPU. If you want to support other m68k variants,
# either modify _target_cpu to suit your needs, or go wild, remove the
# --with-cpu switches and change --disable-multilib with --enable-multilib.
# Be warned that multilib packages will take a lot more time to build, and
# will also require more disk space.

_os="$(
  uname \
    -o)"
if [[ "${_os}" == "Android" ]]; then
  _libc="ndk-sysroot-gcc-compact"
  _compiler="clang"
  _libcompiler="libllvm"
  _sh="dash"
elif [[ "${_os}" == "GNU/Linux" ]]; then
  _libc="glibc"
  _compiler="gcc"
  _libcompiler="libgcc"
  _sh="sh"
elif [[ "${_os}" == "Msys" ]]; then
  _libc="msys2-w32api-runtime"
  _libc_headers="msys2-w32api-headers"
  _compiler="gcc"
  _libcompiler="gcc-libs"
  _sh="sh"
else
  _msg=(
    "Unknown os '${_os}'."
  )
  msg \
    "${_msg[*]}"
  _libc="msys2-w32api-runtime"
  _libc_headers="msys2-w32api-headers"
  _compiler="gcc"
  _libcompiler="gcc-libs"
  _sh="sh"
fi
_target=m68k-elf
_target_cpu=m68000
_pkg=gcc
pkgbase="${_target}-${_pkg}-bootstrap"
pkgname=(
  "${pkgbase}"
)
pkgver=15.2.0
_mpfrver=4.2.2
_mpcver=1.3.1
_gmpver=6.3.0
pkgrel=17
_pkgdesc=(
  "The GNU Compiler Collection."
  "Bootstrap for toolchain building (${_target})"
)
arch=(
  "aarch64"
  "armv8l"
  "armv7l"
  'i686'
  "mips"
  "pentium4"
  "powerpc"
  "x86_64"
)
license=(
  'GPL'
  'LGPL'
  'FDL'
  'custom'
)
url="http://${_pkg}.gnu.org"
depends=(
  "${_target}-binutils>=2.29-1"
  'zlib'
)
makedepends=(
  "${_target}-binutils>=2.29-1"
)
if [[ "${_os}" == "Android" ]]; then
  makedepends+=(
    "libmpc"
  )
fi
conflicts=(
  "${_target}-${_pkg}"
)
options=(
  '!emptydirs'
  '!distcc'
  '!strip'
)
PKGEXT="pkg.tar.xz"
_tarname="${_pkg}-${pkgver}"
source=(
  "https://ftp.gnu.org/gnu/${_pkg}/${_tarname}/${_tarname}.tar.xz"
  "https://ftp.gnu.org/gnu/${_pkg}/${_tarname}/${_tarname}.tar.xz.sig"
  "https://ftp.gnu.org/gnu/mpfr/mpfr-${_mpfrver}.tar.xz"
  "https://ftp.gnu.org/gnu/mpfr/mpfr-${_mpfrver}.tar.xz.sig"
  "https://ftp.gnu.org/gnu/mpc/mpc-${_mpcver}.tar.gz"
  "https://ftp.gnu.org/gnu/mpc/mpc-${_mpcver}.tar.gz.sig"
  "https://ftp.gnu.org/gnu/gmp/gmp-${_gmpver}.tar.xz"
  "https://ftp.gnu.org/gnu/gmp/gmp-${_gmpver}.tar.xz.sig"
)
        
sha256sums=(
  "SKIP"
  "SKIP"
  "SKIP"
  "SKIP"
  "SKIP"
  "SKIP"
  "SKIP"
  "SKIP"
)
validpgpkeys=(
  "13975A70E63C361C73AE69EF6EEB81F8981C74C7"
  "A534BE3F83E241D918280AEB5831D11A0D4DB02A"
  "AD17A21EF8AED8F1CC02DBD9F7D5C9BF765C61E3"
  "343C2FF0FBEE5EC2EDBEF399F3599FF828C67298"
)

prepare() {
  cd \
    "${srcdir}/${_tarname}"

  # symlinks for in-tree build
  ln \
    -s \
    "../mpfr-${_mpfrver}"
  ln \
    -s \
    "../mpc-${_mpcver}"
  ln \
    -s \
    "../gmp-${_gmpver}"

  # hack! - some configure tests for
  # header files using "$CPP $CPPFLAGS"
  sed \
    -i \
    "/ac_cpp=/s/\$CPPFLAGS/\$CPPFLAGS -O2/" \
    {"libiberty","gcc","mpfr-${_mpfrver}","mpc-${_mpcver}","gmp-${_gmpver}"}"/configure"
  mkdir \
    "${srcdir}/${_pkg}-build"
}

build() {
  local \
    _configure_opts=()
  export \
    CFLAGS="${CFLAGS} -Wno-unknown-warning-option"
  _configure_opts+=(
    --prefix="/usr"
    --target="${_target}"
    --enable-languages="c"
    --disable-multilib
    --with-cpu="${_target_cpu}"
    --with-system-zlib
    --with-libgloss
    --without-headers
    --disable-shared
    --disable-nls
  )
  # GCC cannot be built with -Werror=format-security
  export \
    CFLAGS=${CFLAGS//-Werror=format-security/}
  export \
    CXXFLAGS=${CXXFLAGS//-Werror=format-security/}
  cd \
    "${srcdir}/${_pkg}-build"
  "../${_tarname}/configure" \
    "${_configure_opts[@]}"
  make \
    "all-${_pkg}"
}

package_m68k-elf-gcc-bootstrap() {
  local \
    _make_opts=()
  _make_opts+=(
    DESTDIR="${pkgdir}"
  )
  cd \
    "${srcdir}/${_pkg}-build"
  make \
    "${_make_opts[@]}" \
    "install-${_pkg}"
  rm \
    -rf \
    "${pkgdir}/usr/share"
  # strip it manually
  strip \
    "${pkgdir}/usr/bin/"* \
    2>"/dev/null" || \
    true
  find \
    "${pkgdir}/usr/lib" \
    -type \
      "f" \
    -exec \
      "/usr/bin/${_target}-strip" \
        --strip-unneeded \
        {} \; \
        2>"/dev/null" || \
    true
}

