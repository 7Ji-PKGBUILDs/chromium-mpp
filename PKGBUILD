# Maintainer: 7Ji <pugokushin@gmail.com>

# Based on alarm/extra/chromium, with Rockchip MPP support
#   alarm/extra/chromium is based on archlinux/extra/chromium

# Maintainer: Evangelos Foutras <foutrelis@archlinux.org>
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
_pkgver_short=126.0.6478
pkgver=${_pkgver_short}.182
pkgrel=1
_launcher_ver=8
_manual_clone=0
_system_clang=1
pkgdesc="A web browser built for speed, simplicity, and security. Patched with Rockchip MPP support."
arch=('x86_64' 'aarch64')
url="https://www.chromium.org/Home"
license=('BSD-3-Clause')
depends=('gtk3' 'nss' 'alsa-lib' 'xdg-utils' 'libxss' 'libcups' 'libgcrypt'
         'ttf-liberation' 'systemd' 'dbus' 'libpulse' 'pciutils' 'libva'
         'libffi' 'desktop-file-utils' 'hicolor-icon-theme')
makedepends=('python' 'gn' 'ninja' 'clang' 'lld' 'gperf' 'nodejs' 'pipewire'
             'rust' 'qt5-base' 'qt6-base' 'java-runtime-headless' 'git' 'elfutils')
optdepends=('pipewire: WebRTC desktop sharing under Wayland'
            'kdialog: support for native dialogs in Plasma'
            'gtk4: for --gtk-version=4 (GTK4 IME might work better on Wayland)'
            'org.freedesktop.secrets: password storage backend on GNOME / Xfce'
            'kwallet: support for storing passwords in KWallet on Plasma')
options=('!lto') # Chromium adds its own flags for ThinLTO
source=(https://commondatastorage.googleapis.com/chromium-browser-official/chromium-$pkgver.tar.xz
        https://github.com/foutrelis/chromium-launcher/archive/v$_launcher_ver/chromium-launcher-$_launcher_ver.tar.gz
        https://gitlab.com/Matt.Jolly/chromium-patches/-/archive/${pkgver%%.*}/chromium-patches-${pkgver%%.*}.tar.bz2
        allow-ANGLEImplementation-kVulkan.patch
        drop-flag-unsupported-by-clang17.patch
        compiler-rt-adjust-paths.patch
        use-oauth2-client-switches-as-default.patch
        0001-widevine-support-for-arm.patch
        0002-Run-blink-bindings-generation-single-threaded.patch)
sha256sums=('3939f5b3116ebd3cb15ff8c7059888f6b00f4cfa8a77bde983ee4ce5d0eea427'
            '213e50f48b67feb4441078d50b0fd431df34323be15be97c55302d3fdac4483a'
            'daf0df74d2601c35fd66a746942d9ca3fc521ede92312f85af51d94c399fd6e0'
            '8f81059d79040ec598b5fb077808ec69d26d6c9cbebf9c4f4ea48b388a2596c5'
            '028acc97299cec5d1ed9f456bbdc462807fa491277d266db2aa1d405d3cd753d'
            'b3de01b7df227478687d7517f61a777450dca765756002c80c4915f271e2d961'
            'a9b417b96daec33c9059065e15b3a92ae1bf4b59f89d353659b335d9e0379db6'
            'd7e03bb0ca10ed30cf8a427d6d382623930d1cefdeb673ed3ed9babcdfd62503'
            '494d5c3cd7901ec16ff9f66e34f550b2ebc52d02fdc744f444843cfcc79def2b')

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
  '0010-Create-new-fence-when-there-s-no-in-fences.patch'
  '0011-HACK-ozone-wayland-Force-disable-implicit-external-s.patch'
  '0012-HACK-media-capture-linux-Allow-camera-without-suppor.patch'
  '0013-content-gpu-Only-depend-dri-for-X11.patch'
  '0014-media-gpu-sandbox-Only-depend-dri-for-X11.patch'
  '0015-ui-gfx-linux-Force-disabling-modifiers.patch'
  '0016-HACK-ui-x11-Fix-config-choosing-error-with-Mali-DDK.patch'
)
_mpp_commit='164950523c602cc74b4d8d06750215f59d3b3db1'
_mpp_parent="https://github.com/JeffyCN/meta-rockchip/raw/${_mpp_commit}/dynamic-layers/recipes-browser/chromium/chromium_${_pkgver_short}/"
for _mpp_patch in ${_mpp_patches[@]}; do
  source+=("mpp-${_mpp_patch}::${_mpp_parent}${_mpp_patch}")
done
# Local patches on top of the MPP patches
_mpp_arch_patches=('0001-media-sandbox-always-lookup-libv4l2-at-usr-lib-libv4.patch')
source+=(mpp-arch-"${_mpp_arch_patches[0]}")
sha256sums+=(
  # MPP patches
  'e887b626f9bc475effeb7c1a1d5def9cda1bd7bfb5fca18a29f93f26d2b2748f'
  '288def8af27979573e3ed0113d8860a0e823f65988624906a1e4c158264b35aa'
  'bea5d748fd1ef8ba8085f45b001f1bb79d9324dfa695ee9bb5b52bdb9c368e88'
  'c688660f583f2f540b1ad11bdee7fa5d8dbe63fe477091a8c3e92d041e0af5c8'
  '9e2af2d5c9570a73eb2f2f30d5ad1a967d9452a1d234e206384ab5c34704e966'
  'b267867103be6a40ad21fa1293e0548fb84df31e6609e29a93f4375dcf66dbb2'
  'a7dde27ed9fd0f25eba8674ca440886984e623d51a235e975e62f45f13a31a43'
  '79415aa937a21117179aa72d24664ce3b2cff10e0573c0ae7f905d1c00473778'
  'fe86c19576c01d7e57f81856a616582d81d368a68b949d5c46428558d29fd655'
  'fcd809d92a4524c226804dd5168f002cbfdef778a24c4a7bed7d6442e6206aeb'
  '2c89ce54aa82411f41f5bc6cda98067807c80800559658038e59ef07e54e99cc'
  'c094dee92301e07997def872c4918d7f1f908cf6e4ae059b668c31cc9c9452a0'
  '00a5ff2980040b8c711c9f712fbbfc0572e6efd18009b89336da721aa744f566'
  '1feb5af6618b13ba185e5779914d1a7c7b2973c9fbdfde2b2565e2c2a90946d5'
  '27724358b9d6615e73d8f19ef2158a02344616e10bbd4db6f944a2cc3aa1a11e'
  'dc8784e218cb0065119fc688cb79d9a25c644c54032b668e4da4439cafe804cb'
  # Local patches on top of the MPP patches
  'ca909939f0ed659238c796f2ffb109677d51c17ac765ac8a857344fa235c2033'
)
# Possible replacements are listed in build/linux/unbundle/replace_gn_files.py
# Keys are the names in the above script; values are the dependencies in Arch
declare -gA _system_libs=(
  [brotli]=brotli
  [dav1d]=dav1d
  #[ffmpeg]=ffmpeg    # YouTube playback stopped working in Chromium 120
  [flac]=flac
  [fontconfig]=fontconfig
  [freetype]=freetype2
  [harfbuzz-ng]=harfbuzz
  [icu]=icu
  #[jsoncpp]=jsoncpp  # needs libstdc++
  #[libaom]=aom
  #[libavif]=libavif  # needs https://github.com/AOMediaCodec/libavif/commit/5410b23f76
  [libdrm]=
  [libjpeg]=libjpeg
  [libpng]=libpng
  #[libvpx]=libvpx
  [libwebp]=libwebp
  [libxml]=libxml2
  [libxslt]=libxslt
  [opus]=opus
  #[re2]=re2          # needs libstdc++
  #[snappy]=snappy    # needs libstdc++
  #[woff2]=woff2      # needs libstdc++
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

_alarm_makeflags() {
  case "${CARCH}" in
    'armv7h')
      export ALARM_NINJA_JOBS="4"
      export MAKEFLAGS="-j4"
      ;;
    'aarch64') # Allow build to set march and options on AArch64 (crc, crypto)
      CFLAGS="${CFLAGS/-march=armv8-a}"
      CXXFLAGS="${CFLAGS}"
      ;;
  esac
}

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

  # use system eu-strip 
  ln -sf /usr/bin/eu-strip buildtools/third_party/eu-strip/bin/eu-strip

  # Update flags
  _alarm_makeflags

  CFLAGS="${CFLAGS/_FORTIFY_SOURCE=3/_FORTIFY_SOURCE=2}" && CXXFLAGS="$CFLAGS"

  # https://crbug.com/893950
  sed -i -e 's/\<xmlMalloc\>/malloc/' -e 's/\<xmlFree\>/free/' \
         -e '1i #include <cstdlib>' \
    third_party/blink/renderer/core/xml/*.cc \
    third_party/blink/renderer/core/xml/parser/xml_document_parser.cc \
    third_party/libxml/chromium/*.cc \
    third_party/maldoca/src/maldoca/ole/oss_utils.h

  # Use the --oauth2-client-id= and --oauth2-client-secret= switches for
  # setting GOOGLE_DEFAULT_CLIENT_ID and GOOGLE_DEFAULT_CLIENT_SECRET at
  # runtime -- this allows signing into Chromium without baked-in values
  patch -Np1 -i ../use-oauth2-client-switches-as-default.patch

  # Upstream fixes
  patch -Np1 -i ../allow-ANGLEImplementation-kVulkan.patch

  # Drop compiler flag that needs newer clang
  patch -Np1 -i ../drop-flag-unsupported-by-clang17.patch

  # Allow libclang_rt.builtins from compiler-rt >= 16 to be used
  patch -Np1 -i ../compiler-rt-adjust-paths.patch

  # Fixes for building with libstdc++ instead of libc++
  patch -Np1 -i ../chromium-patches-*/chromium-117-material-color-include.patch

  # MPP Patches
  for _mpp_patch in ${_mpp_patches[@]}; do
    patch -Np1 -i ../mpp-${_mpp_patch}
  done

  # Link to system tools required by the build
  mkdir -p third_party/node/linux/node-linux-x64/bin
  ln -s /usr/bin/node third_party/node/linux/node-linux-x64/bin/
  ln -s /usr/bin/java third_party/jdk/current/bin/

  if (( !_system_clang )); then
    # Use prebuilt rust as system rust cannot be used due to the error:
    #   error: the option `Z` is only accepted on the nightly compiler
    ./tools/rust/update_rust.py

    # To link to rust libraries we need to compile with prebuilt clang
    ./tools/clang/scripts/update.py
  fi

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

  if (( _system_clang )); then
    export CC=clang
    export CXX=clang++
    export AR=ar
    export NM=nm
  else
    local _clang_path="$PWD/third_party/llvm-build/Release+Asserts/bin"
    export CC=$_clang_path/clang
    export CXX=$_clang_path/clang++
    export AR=$_clang_path/llvm-ar
    export NM=$_clang_path/llvm-nm
  fi

  local _flags=(
    'custom_toolchain="//build/toolchain/linux/unbundle:default"'
    'host_toolchain="//build/toolchain/linux/unbundle:default"'
    'clang_use_default_sample_profile=false'
    'use_allocator="none"'
    'is_official_build=true' # implies is_cfi=true on x86_64
    'symbol_level=0' # sufficient for backtraces on x86(_64)
    'is_cfi=false'
    'treat_warnings_as_errors=false'
    'disable_fieldtrial_testing_config=true'
    'blink_enable_generated_code_formatting=false'
    'ffmpeg_branding="Chrome"'
    'proprietary_codecs=true'
    'rtc_use_pipewire=true'
    'link_pulseaudio=true'
    'use_custom_libcxx=true' # https://github.com/llvm/llvm-project/issues/61705
    'use_gold=false'
    'use_sysroot=false'
    'use_system_libffi=true'
    'enable_hangout_services_extension=true'
    'enable_widevine=true'
    'enable_nacl=false'
    'use_qt6=true'
    'moc_qt6_path="/usr/lib/qt6"'
    "google_api_key=\"$_google_api_key\""
  )

  if [[ -n ${_system_libs[icu]+set} ]]; then
    _flags+=('icu_use_data_file=false')
  fi

  if (( _system_clang )); then
     local _clang_version=$(
       clang --version | grep -m1 version | sed 's/.* \([0-9]\+\).*/\1/')

    _flags+=(
      'clang_base_path="/usr"'
      'clang_use_chrome_plugins=false'
      "clang_version=\"$_clang_version\""
      'chrome_pgo_phase=0' # needs newer clang to read the bundled PGO profile
    )

    # Allow the use of nightly features with stable Rust compiler
    # https://github.com/ungoogled-software/ungoogled-chromium/pull/2696#issuecomment-1918173198
    export RUSTC_BOOTSTRAP=1

    _flags+=(
      'rust_sysroot_absolute="/usr"'
      "rustc_version=\"$(rustc --version)\""
    )
  fi

  if [[ $CARCH == "armv7h" ]]; then
    _flags+=('use_thin_lto=false')
  fi

  _flags+=(
    'use_v4l2_codec=true'
    'use_v4lplugin=true'
    'use_linux_v4l2_only=true'
  )

  # Update flags, in case prepare() was skipped
  _alarm_makeflags

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

  echo "Building with CFLAGS: ${CFLAGS}"
  echo "Building with CXXFLAGS: ${CXXFLAGS}"
  echo "Building with CPPFLAGS: ${CPPFLAGS}"

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
    libqt6_shim.so
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
