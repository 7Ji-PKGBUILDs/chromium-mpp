# Maintainer: 7Ji <pugokushin@gmail.com>

# Based on alarm/extra/chromium, with Rockchip MPP support
#   alarm/extra/chromium is based on archlinux/extra/chromium

# Maintainer: Evangelos Foutras <evangelos@foutrelis.com>
# Contributor: Pierre Schmitz <pierre@archlinux.de>
# Contributor: Jan "heftig" Steffens <jan.steffens@gmail.com>
# Contributor: Daniel J Griffiths <ghost1227@archlinux.us>

# ALARM: Kevin Mihelich <kevin@archlinuxarm.org>
#  - makeflags to -j4 on v7, RAM constraints
#  - let build set march on AArch64
#  - is_cfi=false, use_gold=false
#  - clang_use_default_sample_profile=false, AFDO breaks distcc
#  - use_allocator="none", tcmalloc breaks ARM
#  - patch and rebuild bundled eu-strip
#  - disable LTO on v7

highmem=1

_pkgname=chromium
pkgname=${_pkgname}-mpp
_pkgver_short=119.0.6045 # MPP patches are released for x.y.z
pkgver=${_pkgver_short}.199
pkgrel=1
_launcher_ver=8
_manual_clone=0
pkgdesc="A web browser built for speed, simplicity, and security. Patched with Rockchip MPP support."
arch=('aarch64')
url="https://www.chromium.org/Home"
license=('BSD')
depends=('gtk3' 'nss' 'alsa-lib' 'xdg-utils' 'libxss' 'libcups' 'libgcrypt'
         'ttf-liberation' 'systemd' 'dbus' 'libpulse' 'pciutils' 'libva'
         'libffi' 'desktop-file-utils' 'hicolor-icon-theme')
makedepends=('python' 'gn' 'ninja' 'clang' 'lld' 'gperf' 'nodejs' 'pipewire'
             'qt5-base' 'java-runtime-headless' 'git')
optdepends=('pipewire: WebRTC desktop sharing under Wayland'
            'kdialog: support for native dialogs in Plasma'
            'qt5-base: enable Qt5 with --enable-features=AllowQt'
            'gtk4: for --gtk-version=4 (GTK4 IME might work better on Wayland)'
            'org.freedesktop.secrets: password storage backend on GNOME / Xfce'
            'kwallet: support for storing passwords in KWallet on Plasma')
options=('!lto') # Chromium adds its own flags for ThinLTO
source=(https://commondatastorage.googleapis.com/chromium-browser-official/chromium-$pkgver.tar.xz
        https://github.com/foutrelis/chromium-launcher/archive/v$_launcher_ver/chromium-launcher-$_launcher_ver.tar.gz
        https://gitlab.com/Matt.Jolly/chromium-patches/-/archive/${pkgver%%.*}/chromium-patches-${pkgver%%.*}.tar.bz2
        libxml2-2.12.patch
        icu-74.patch
        REVERT-disable-autoupgrading-debug-info.patch
        use-oauth2-client-switches-as-default.patch
        0001-widevine-support-for-arm.patch
        0002-Run-blink-bindings-generation-single-threaded.patch
        0003-Fix-eu-strip-build-for-newer-GCC.patch
        0004-Optimize-eu-strip-building-logic.patch
        git+https://sourceware.org/git/elfutils.git)
sha256sums=('b1ae62beb7907d99802b74821d5198bd54a7456df1116d317da653bde8ce6388'
            '213e50f48b67feb4441078d50b0fd431df34323be15be97c55302d3fdac4483a'
            '09ecf142254525ddb9c2dbbb2c71775e68722412923a5a9bba5cc2e46af8d087'
            '1808df5ba4d1e2f9efa07ac6b510bec866fa6d60e44505d82aea3f6072105a71'
            'ff9ebd86b0010e1c604d47303ab209b1d76c3e888c423166779cefbc22de297f'
            '1b782b0f6d4f645e4e0daa8a4852d63f0c972aa0473319216ff04613a0592a69'
            'e393174d7695d0bafed69e868c5fbfecf07aa6969f3b64596d0bae8b067e1711'
            'b5bb3d0e2cd06aa92bb0ea62d6915dac1635cee79e9e1405cf17fe471baa393e'
            '01c8742f987e158245959561db7f7529254a81491954174be2ef8a4f226cbf42'
            '4b543a4f12b8f00765d3a2812c5452a60c5c07babc00fe2036edd6467462ecfe'
            'ac940ff39aae2dfe73967195eeb39fabe439eaa4ba5d5cd3f30ca1fb2030252c'
            'SKIP')

if (( _manual_clone )); then
  source[0]=fetch-chromium-release
  makedepends+=('python-httplib2' 'python-pyparsing' 'python-six')
fi

install=${pkgname}.install
provides=("chromium=${pkgver}" "chromedriver=${pkgver}")
conflicts=('chromium' 'chromedriver')
depends+=('libv4l-rkmpp')
optdepends+=('libmali: blob drivers needed for rendering after MPP decoding')
_mpp_patches=(
  '0001-HACK-media-Support-V4L2-video-decoder.patch'
  '0002-HACK-media-gpu-v4l2-Enable-V4L2-VEA.patch'
  '0003-media-gpu-v4l2-Gen-libv4l2_stubs.patch'
  '0004-media-gpu-v4l2-Support-libv4l2-plugins.patch'
  '0005-media-capture-linux-Support-libv4l2-plugins.patch'
  '0006-cld3-Avoid-unaligned-accesses.patch'
  '0007-media-gpu-v4l2-Use-POLLIN-for-pending-event.patch'
  '0008-media-capture-linux-Prefer-using-the-first-device.patch'
  '0009-media-gpu-v4l2-Fix-compile-error-when-ozone-not-enab.patch'
  '0010-ui-events-ozone-Define-SW_PEN_INSERTED-for-old-kerne.patch'
  '0011-Create-new-fence-when-there-s-no-in-fences.patch'
  '0012-HACK-ozone-wayland-Force-disable-implicit-external-s.patch'
  '0013-HACK-media-capture-linux-Allow-camera-without-suppor.patch'
  '0014-content-gpu-Only-depend-dri-for-X11.patch'
  '0015-media-gpu-sandbox-Only-depend-dri-for-X11.patch'
  '0016-HACK-ui-gl-Force-enabling-validating-command-decoder.patch'
  '0017-ui-gfx-linux-Force-disabling-modifiers.patch'
)
_mpp_commit='7f491ad965b9b499c39f77c75940f2e3222f5ea9'
_mpp_parent="https://github.com/JeffyCN/meta-rockchip/raw/${_mpp_commit}/dynamic-layers/recipes-browser/chromium/chromium_${_pkgver_short}/"
for _mpp_patch in ${_mpp_patches[@]}; do
  source+=("mpp-${_mpp_patch}::${_mpp_parent}${_mpp_patch}")
done
# Local patches on top of the MPP patches
_mpp_arch_patches=('0001-hwdecsb-always-open-libv4l2-at-usr-lib-libv4l2.so.patch')
source+=(mpp-arch-"${_mpp_arch_patches[0]}")
sha256sums+=(
  # MPP patches
  '4f71d387796ad3ee434b0cb0aa5d1b6b29c519a360ef7982086272e2c612a1fc'
  'dab7a4b11db893cbe38143831d7e572d59913dc78d2a3c8e6c25bf287cf878e4'
  '5a03cc7b35b91a48d6cc65830d96432819987dbd5e5cb988e6cf4b51260b48d9'
  '6a3a669b916dcc9c815c2be63e766c5f86143348a39951095c165752ce276991'
  'd7420f4dfafdc5f0e2b7e6f5a67e52d43381d3a687fa4d42788dd1810ca95e6f'
  '73cc17a4b93d36e4df4528dbdc90a81b053e2e08489bff10556be64892577bb6'
  '16d926730fab9a7de2b48b6675584be94e4edcac893eadd2d94ed412f4f4392e'
  'a8947ed6d51cebd45418b81d437eea526df87c2c0e024369c32452d8195a1d45'
  '62de89837cb0e3d9bdf8b28206cc9ce24f07dc4c9fd4a61ba9ed67bf5bc2adf9'
  '4b4acc74009d2d8e45a34c7bc473cfabc0d53a6545e6b5a37bd1c469573e121b'
  '89f5d2155f51f70d95f7f27c8a1f36b8f4f189267dde62ff7e568a54eace2908'
  '545dcd39f1ac5b6ecd4692af5658d30f9434baa48fe4507739a2ee5326fe3ae9'
  '2aad7b9c7ddbe09afffbf867bbe126d7e30cb7c1d246fb0c015be194d26d6be7'
  '9ac085d9bdc588a3d3c7a60435abfb7fe24330e09d9224878a10277ef40a0a8a'
  'ec9ffd8c2d64310ef70517e177ba5ffe17395648fe2b4cb60dd07c8bed67ddc9'
  '8ec9a151b16a72375e94723b08fddb493f9e8147935fa6acc0dc0ddb2ffcd769'
  'a9dfa1308361692948efb0067d20af312dd4857cba0146d6ebc5a4b8182825b4'
  # Local patches on top of the MPP patches
  'f2725c7a2c79482dded2effc3be2cebbf6843154692d1e2f33b6477b2b001656'
)
# Possible replacements are listed in build/linux/unbundle/replace_gn_files.py
# Keys are the names in the above script; values are the dependencies in Arch
declare -gA _system_libs=(
  #[brotli]=brotli
  [dav1d]=dav1d
  [ffmpeg]=ffmpeg
  [flac]=flac
  [fontconfig]=fontconfig
  [freetype]=freetype2
  [harfbuzz-ng]=harfbuzz
  [icu]=icu
  [jsoncpp]=jsoncpp
  [libaom]=aom
  #[libavif]=libavif  # needs https://github.com/AOMediaCodec/libavif/commit/5410b23f76
  [libdrm]=
  [libjpeg]=libjpeg
  [libpng]=libpng
  [libvpx]=libvpx
  [libwebp]=libwebp
  [libxml]=libxml2
  [libxslt]=libxslt
  [opus]=opus
  [re2]=re2
  [snappy]=snappy
  [woff2]=woff2
  [zlib]=minizip
)
_unwanted_bundled_libs=(
  $(printf "%s\n" ${!_system_libs[@]} | sed 's/^libjpeg$/&_turbo/')
)
depends+=(${_system_libs[@]})

# Google API keys (see https://www.chromium.org/developers/how-tos/api-keys)
# Note: These are for Arch Linux use ONLY. For your own distribution, please
# get your own set of keys.
#
# Starting with Chromium 89 (2021-03-02) the OAuth2 credentials have been left
# out: https://archlinux.org/news/chromium-losing-sync-support-in-early-march/
#  - Arch Linux ARM has obtained permission to use the Arch Linux keys.
_google_api_key=AIzaSyDwr302FpOSkGRpLlUpPThNTDPbXcIn_FM

prepare() {
  if (( _manual_clone )); then
    ./fetch-chromium-release $pkgver
  fi
  cd chromium-$pkgver

  # Allow building against system libraries in official builds
  sed -i 's/OFFICIAL_BUILD/GOOGLE_CHROME_BUILD/' \
    tools/generate_shim_headers/generate_shim_headers.py

  # Arch Linux ARM fixes
  patch -p1 -i ../0001-widevine-support-for-arm.patch
  patch -p1 -i ../0002-Run-blink-bindings-generation-single-threaded.patch
  patch -p1 -i ../0003-Fix-eu-strip-build-for-newer-GCC.patch
  patch -p1 -i ../0004-Optimize-eu-strip-building-logic.patch

  if [[ $CARCH == "armv7h" ]]; then
    export ALARM_NINJA_JOBS="4"
    export MAKEFLAGS="-j4"
  fi

  # Allow build to set march and options on AArch64 (crc, crypto)
  [[ $CARCH == "aarch64" ]] && CFLAGS=`echo $CFLAGS | sed -e 's/-march=armv8-a//'` && CXXFLAGS="$CFLAGS"

  # https://crbug.com/893950
  sed -i -e 's/\<xmlMalloc\>/malloc/' -e 's/\<xmlFree\>/free/' \
    third_party/blink/renderer/core/xml/*.cc \
    third_party/blink/renderer/core/xml/parser/xml_document_parser.cc \
    third_party/libxml/chromium/*.cc \
    third_party/maldoca/src/maldoca/ole/oss_utils.h

  # Use the --oauth2-client-id= and --oauth2-client-secret= switches for
  # setting GOOGLE_DEFAULT_CLIENT_ID and GOOGLE_DEFAULT_CLIENT_SECRET at
  # runtime -- this allows signing into Chromium without baked-in values
  patch -Np1 -i ../use-oauth2-client-switches-as-default.patch

  # Upstream fixes
  patch -Np1 -i ../libxml2-2.12.patch

  # Fix build with ICU 74
  patch -Np1 -i ../icu-74.patch

  # Revert addition of compiler flag that needs newer clang
  patch -Rp1 -i ../REVERT-disable-autoupgrading-debug-info.patch

  # Fixes for building with libstdc++ instead of libc++
  patch -Np1 -i ../chromium-patches-*/chromium-114-ruy-include.patch
  patch -Np1 -i ../chromium-patches-*/chromium-114-vk_mem_alloc-include.patch
  patch -Np1 -i ../chromium-patches-*/chromium-117-material-color-include.patch
  patch -Np1 -i ../chromium-patches-*/chromium-119-FragmentDataIterator-std.patch
  patch -Np1 -i ../chromium-patches-*/chromium-119-at-spi-variable-consumption.patch
  patch -Np1 -i ../chromium-patches-*/chromium-119-clang16.patch

  # MPP Patches
  for _mpp_patch in ${_mpp_patches[@]}; do
    patch -Np1 -i ../mpp-${_mpp_patch}
  done

  patch -Np1 -i ../mpp-arch-${_mpp_arch_patches[0]}

  # Link to system tools required by the build
  mkdir -p third_party/node/linux/node-linux-x64/bin
  ln -s /usr/bin/node third_party/node/linux/node-linux-x64/bin/
  ln -s /usr/bin/java third_party/jdk/current/bin/

  # Remove bundled libraries for which we will use the system copies; this
  # *should* do what the remove_bundled_libraries.py script does, with the
  # added benefit of not having to list all the remaining libraries
  local _lib
  for _lib in ${_unwanted_bundled_libs[@]}; do
    find "third_party/$_lib" -type f \
      \! -path "third_party/$_lib/chromium/*" \
      \! -path "third_party/$_lib/google/*" \
      \! -path "third_party/harfbuzz-ng/utils/hb_scoped.h" \
      \! -regex '.*\.\(gn\|gni\|isolate\)' \
      -delete
  done

  ./build/linux/unbundle/replace_gn_files.py \
    --system-libraries "${!_system_libs[@]}"
}

build() {
  make -C chromium-launcher-$_launcher_ver

  cd chromium-$pkgver

  # Rebuild eu-strip
  pushd buildtools/third_party/eu-strip
  elfutils_git="${srcdir}/elfutils/.git" ./build.sh
  popd

  export CC=clang
  export CXX=clang++
  export AR=ar
  export NM=nm

  local _flags=(
    'custom_toolchain="//build/toolchain/linux/unbundle:default"'
    'host_toolchain="//build/toolchain/linux/unbundle:default"'
    'clang_base_path="/usr"'
    'clang_use_chrome_plugins=false'
    'clang_use_default_sample_profile=false'
    'use_allocator="none"'
    'is_official_build=true' # implies is_cfi=true on x86_64
    'symbol_level=0' # sufficient for backtraces on x86(_64)
    'is_cfi=false'
    'chrome_pgo_phase=0'
    'treat_warnings_as_errors=false'
    'disable_fieldtrial_testing_config=true'
    'blink_enable_generated_code_formatting=false'
    'ffmpeg_branding="Chrome"'
    'proprietary_codecs=true'
    'rtc_use_pipewire=true'
    'link_pulseaudio=true'
    'use_custom_libcxx=false'
    'use_gold=false'
    'use_sysroot=false'
    'use_system_libffi=true'
    'enable_hangout_services_extension=true'
    'enable_widevine=true'
    'enable_nacl=false'
    'enable_rust=false'
    "google_api_key=\"$_google_api_key\""
  )

  if [[ -n ${_system_libs[icu]+set} ]]; then
    _flags+=('icu_use_data_file=false')
  fi

  if [[ $CARCH == "armv7h" ]]; then
    _flags+=('use_thin_lto=false')
  fi

  _flags+=(
    'use_v4l2_codec=true'
    'use_v4lplugin=true'
    'use_linux_v4l2_only=true'
  )

  # Facilitate deterministic builds (taken from build/config/compiler/BUILD.gn)
  CFLAGS+='   -Wno-builtin-macro-redefined'
  CXXFLAGS+=' -Wno-builtin-macro-redefined'
  CPPFLAGS+=' -D__DATE__=  -D__TIME__=  -D__TIMESTAMP__='

  # Do not warn about unknown warning options
  CFLAGS+='   -Wno-unknown-warning-option'
  CXXFLAGS+=' -Wno-unknown-warning-option'

  # Let Chromium set its own symbol level
  CFLAGS=${CFLAGS/-g }
  CXXFLAGS=${CXXFLAGS/-g }

  # https://github.com/ungoogled-software/ungoogled-chromium-archlinux/issues/123
  CFLAGS=${CFLAGS/-fexceptions}
  CFLAGS=${CFLAGS/-fcf-protection}
  CXXFLAGS=${CXXFLAGS/-fexceptions}
  CXXFLAGS=${CXXFLAGS/-fcf-protection}

  # This appears to cause random segfaults when combined with ThinLTO
  # https://bugs.archlinux.org/task/73518
  CFLAGS=${CFLAGS/-fstack-clash-protection}
  CXXFLAGS=${CXXFLAGS/-fstack-clash-protection}

  # https://crbug.com/957519#c122
  CXXFLAGS=${CXXFLAGS/-Wp,-D_GLIBCXX_ASSERTIONS}

  gn gen out/Release --args="${_flags[*]}"
  ninja -C out/Release chrome chrome_sandbox chromedriver.unstripped
}

package() {
  cd chromium-launcher-$_launcher_ver
  make PREFIX=/usr DESTDIR="$pkgdir" install
  install -Dm644 LICENSE \
    "$pkgdir/usr/share/licenses/chromium/LICENSE.launcher"

  cd ../chromium-$pkgver

  install -D out/Release/chrome "$pkgdir/usr/lib/chromium/chromium"
  install -Dm4755 out/Release/chrome_sandbox "$pkgdir/usr/lib/chromium/chrome-sandbox"

  install -Dm644 chrome/installer/linux/common/desktop.template \
    "$pkgdir/usr/share/applications/chromium.desktop"
  install -Dm644 chrome/app/resources/manpage.1.in \
    "$pkgdir/usr/share/man/man1/chromium.1"
  sed -i \
    -e 's/@@MENUNAME@@/Chromium/g' \
    -e 's/@@PACKAGE@@/chromium/g' \
    -e 's/@@USR_BIN_SYMLINK_NAME@@/chromium/g' \
    "$pkgdir/usr/share/applications/chromium.desktop" \
    "$pkgdir/usr/share/man/man1/chromium.1"

  install -Dm644 chrome/installer/linux/common/chromium-browser/chromium-browser.appdata.xml \
    "$pkgdir/usr/share/metainfo/chromium.appdata.xml"
  sed -ni \
    -e 's/chromium-browser\.desktop/chromium.desktop/' \
    -e '/<update_contact>/d' \
    -e '/<p>/N;/<p>\n.*\(We invite\|Chromium supports Vorbis\)/,/<\/p>/d' \
    -e '/^<?xml/,$p' \
    "$pkgdir/usr/share/metainfo/chromium.appdata.xml"

  local toplevel_files=(
    chrome_100_percent.pak
    chrome_200_percent.pak
    chrome_crashpad_handler
    libqt5_shim.so
    resources.pak
    v8_context_snapshot.bin

    # ANGLE
    libEGL.so
    libGLESv2.so

    # SwiftShader ICD
    libvk_swiftshader.so
    libvulkan.so.1
    vk_swiftshader_icd.json
  )

  if [[ -z ${_system_libs[icu]+set} ]]; then
    toplevel_files+=(icudtl.dat)
  fi

  cp "${toplevel_files[@]/#/out/Release/}" "$pkgdir/usr/lib/chromium/"
  install -Dm644 -t "$pkgdir/usr/lib/chromium/locales" out/Release/locales/*.pak

  for size in 24 48 64 128 256; do
    install -Dm644 "chrome/app/theme/chromium/product_logo_$size.png" \
      "$pkgdir/usr/share/icons/hicolor/${size}x${size}/apps/chromium.png"
  done

  for size in 16 32; do
    install -Dm644 "chrome/app/theme/default_100_percent/chromium/product_logo_$size.png" \
      "$pkgdir/usr/share/icons/hicolor/${size}x${size}/apps/chromium.png"
  done

  install -Dm644 LICENSE "$pkgdir/usr/share/licenses/chromium/LICENSE"
}

# vim:set ts=2 sw=2 et:
