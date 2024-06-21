# Maintainer:  dreieck (https://aur.archlinux.org/account/dreieck)
# Contributpr: enricostn (https://aur.archlinux.org/account/enricostn)

_gogname="day_of_the_tentacle_remastered"
pkgbase="day-of-the-tentacle-remastered-gog-bin"
pkgname=(
  "day-of-the-tentacle-remastered-bin"
  "maniac-mansion-data-bin"
  "maniac-mansion"
)
_pkgver_game="1.4.1"
_pkgver_gog="2.1.0.2"
pkgver=1.4.1+gog2.1.0.2
pkgrel=5
epoch=1
arch=(
  'i686'
  'x86_64'
)
pkgdesc="Day of the Tentacle Remastered, plus Maniac Mansion (release 3 from 1989). You need a GOG account and have purchased the file through GOG; PKGBUILD might ask you for GOG login."
url="https://www.doublefine.com/games/day-of-the-tentacle-remastered"
license=(
  'LicenseRef-Proprietary'
  'LicenseRef-GOG_User_Agreement'
  'Zlib'
  'GPL-2.0-or-later'
  'GPL-3.0-or-later'
)
groups=('games')
makedepends=(
  'gogextract'
  'lgogdownloader'
  'optipng'
  'untangle' # To extract Maniac Mansion as standalone game
  # 'wyvern'
)
checkdepends=()
changelog="dott.changelog"
_archivename="gog_day_of_the_tentacle_remastered_${_pkgver_gog}.sh"
source=(
  "maniac-mansion.sh"
  "Maniac_Mansion_Icon.png::https://www.classicgaming.cc/pc/maniac-mansion/images/icons/icon-maniac-mansion-logo-128x128.png"
  "dott.desktop"
  "dott.changelog"
  "maniac-mansion.desktop"
  "license-note-proprietary.txt"
)
sha256sums=(
  '387d1bdd548a3beb142216b8ae60144102275666238edf07c183999003a865ac'  # maniac-mansion.sh
  '7a6cf4eee9695250bd38754bc93f3ddecc0ce69dcfb149d750ec1d162dfa1d30'  # Maniac_Mansion_Icon.png
  'f3f6ce50a0dabaa344b66d7c70c828714a45944f2043170530b0cba715328d0d'  # dott.desktop
  'b3cc9cfcc82ac701a9a543c4b8d351786b0c8e24c38386d762a9ad6ffa167cb4'  # dott.changelog
  '54e01410d29c2ed264837f92acbc16173b4bd1987697ddc74c1be6221b9a3c4a'  # maniac-mansion.desktop
  'c75b0fc50592bf1bccbd9556efdd52894c08ffde633d35e6f2f1dfc50ce1b55e'  # license-note-proprietary.txt
)

_download_game() {
  ## Argumens:
  #  $1: Gamename
  #  $2: Output directory

  if [ "$#" -lt 2 ]; then
    error "${FUNCNAME[0]}: Need to specify two arguments: 1.: GOG Game name (e.g. '${_gogname}'), 2.: Output directory (use '\${SRCDEST}')."
    return 11
  fi

  local _gamename
  local _targetdir

  _gamename="${1#*://}" # Strip away possibly leading '://'.
  _targetdir="$2"

  /usr/bin/lgogdownloader --verbosity 2 --directory "${_targetdir}" --no-color --save-serials --save-logo --save-icon --save-game-details-json --save-product-json --include i,p,l --include-hidden-products --lowspeed-timeout 60 --lowspeed-rate 128 --progress-interval 2000 --platform linux --game "${_gamename}" --download
}

prepare() {
  cd "${srcdir}"
  if [ -e "${_gogname}" ]; then
    rm "${_gogname}"
  fi
  if [ -e "data.zip" ]; then
    rm "data.zip"
  fi
  if [ -e "unpack" ]; then
    rm -Rf "unpack"
  fi
  if [ -e "maniac-mansion" ]; then
    rm -Rf "maniac-mansion"
  fi

  msg2 "Downloading GOG game '${_gogname}' ..."
  _download_game "${_gogname}" "${SRCDEST}"
  ln -s "${SRCDEST}/${_gogname}" "${srcdir}/${_gogname}"

  mkdir -p unpack

  msg2 "Extracting game data ..."
  gogextract -g "${srcdir}/unpack" "${srcdir}/${_gogname}/${_archivename}"

  msg2 "Extracting Maniac Mansion ..."
  mkdir -p maniac-mansion
  cd maniac-mansion
  untangle -x -F 'maniac/*' "${srcdir}/unpack/game"/tenta.cle
}

pkgver() {
  cd "${srcdir}/unpack"
  source support/gog_com.shlib
  _ver_developer="$(get_gameinfo 3)"
  _ver_gog="$(get_gameinfo 2)"
  printf '%s' "${_ver_developer}+gog${_ver_gog}"
}

build() {
  msg2 "Size-optimising 'icon.png' ..."
  optipng -o9 "${srcdir}/unpack/support/icon.png"
  msg2 "Size-optimising 'Maniac_Mansion_Icon.png' ..."
  optipng -o9 -clobber --out "${srcdir}/maniac-mansion.png" "${srcdir}/Maniac_Mansion_Icon.png"
}

package_day-of-the-tentacle-remastered-bin() {
  pkgdesc="Day of the Tentacle Remastered. You need a GOG account and have purchased the file through GOG; PKGBUILD might ask you for GOG login."
  arch=(
    'i686'
    'x86_64'
  )
  depends=()
  depends_i686=(
    'alsa-lib'
    'mesa-libgl'
  )
  depends_x86_64=(
    'lib32-alsa-lib'
    'lib32-libudev0-shim'
    'lib32-mesa-libgl'
  )
  provides=(
    "dott=20161228"  # Originally, this package was named 'dott', version 20161228.
    "day-of-the-tentacle-remastered-gog-bin=${pkgver}"
    "day-of-the-tentacle-remastered=${pkgver}"
    "day-of-the-tentacle"
  )
  conflicts=(
    "dott"
    "day-of-the-tentacle-remastered-gog-bin"
    "day-of-the-tentacle-remastered"
    "day-of-the-tentacle"
  )
  replaces=(
    "day-of-the-tentacle-remastered-gog-bin=1.4.1+gog2.1.0.2"  # Since we made a split package, we now name the actual package not anymore with `-gog`, only the package base. Since GOG specifities are only relevant for source download and extraction, not for installation.
    "dott<=20161228"  # Originally, this package was named 'dott', version 20161228.
  )

  install -Dvm755 -t "${pkgdir}/usr/lib/dott"     "${srcdir}/unpack/game/Dott"
  install -Dvm644 -t "${pkgdir}/usr/lib/dott"     "${srcdir}/unpack/game"/{tenta.cle,controllerdef.txt}
  install -Dvm755 -t "${pkgdir}/usr/lib/dott/lib" "${srcdir}/unpack/game/lib"/*

  install -dvm755 "${pkgdir}/usr/bin"
  ln -sv "/usr/lib/dott/Dott" "${pkgdir}/usr/bin/dott"

  install -Dvm644 -t "${pkgdir}/usr/share/applications" "${srcdir}/dott.desktop"
  install -Dvm644 "${srcdir}/unpack/support/icon.png" "${pkgdir}/usr/share/pixmaps/dott.png"

  install -Dvm644 -t "${pkgdir}/usr/share/doc/dott" "${srcdir}/${_gogname}"/{game-details.json,product.json,logo.jpg} "${srcdir}/unpack"/{gameinfo,docs/installer_readme.txt,support/support_notice.txt,game/readme.txt}
  install -Dvm644  "${srcdir}/unpack/docs/End User License Agreement.txt"  "${pkgdir}/usr/share/licenses/${pkgname}/Tools_End_User_License_Agreements.txt"
  install -Dvm644  "${srcdir}/license-note-proprietary.txt"                "${pkgdir}/usr/share/licenses/${pkgname}/license-note-proprietary.txt"
  ln -svr          "${pkgdir}/usr/share/licenses/${pkgname}"/*             "${pkgdir}/usr/share/doc/dott"/
}

package_maniac-mansion-data-bin() {
  pkgdesc="Data files of 'Maniac Mansion', release 3 from 1989. To be played with ScummVM."
  arch=('any')
  optdepends=(
    "maniac-mansion: To run the game via '/usr/bin/maniac-mansion'."
    'scummvm: To run the game manually with ScummVM.'
  )
  provides=(
    "maniac-mansion-data=v2"
    "maniac-mansion-data=release3"
  )
  conflicts=(
    "maniac-mansion-data"
  )
  replaces=(
    "maniac-mansion-original-data-bin=1.4.1+gog2.1.0.2"  # Due to package renaming.
  )

  install -Dvm644 -t "${pkgdir}/usr/lib/maniac-mansion" "${srcdir}/maniac-mansion/maniac"/*
  install -Dvm644  "${srcdir}/maniac-mansion.png"            "${pkgdir}/usr/share/pixmaps/maniac-mansion.png"
  install -Dvm644  "${srcdir}/license-note-proprietary.txt"  "${pkgdir}/usr/share/licenses/${pkgname}/license-note-proprietary.txt"
}

package_maniac-mansion() {
  pkgdesc="Standalone ScummVM based launcher script for the original game 'Maniac Mansion'."
  arch=('any')
  license=("GPL-3.0-or-later")
  depends=(
    "maniac-mansion-data"
    "scummvm"
    "sh"
  )
  provides=(
    "maniac-mansion"
  )
  conflicts=(
    "maniac-mension"
  )
  replaces=(
    "maniac-mansion-original=1.4.1+gog2.1.0.2"  # Due to package renaming.
  )

  install -Dvm755 "${srcdir}/maniac-mansion.sh"  "${pkgdir}/usr/bin/maniac-mansion"
  install -Dvm644 -t "${pkgdir}/usr/share/applications" "${srcdir}/maniac-mansion.desktop"
}
