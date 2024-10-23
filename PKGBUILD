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
_pkgver_short=129.0.6668
pkgver=${_pkgver_short}.100
pkgrel=2
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
             'rust' 'rust-bindgen' 'qt5-base' 'qt6-base' 'java-runtime-headless'
             'git' 'elfutils')
optdepends=('pipewire: WebRTC desktop sharing under Wayland'
            'kdialog: support for native dialogs in Plasma'
            'gtk4: for --gtk-version=4 (GTK4 IME might work better on Wayland)'
            'org.freedesktop.secrets: password storage backend on GNOME / Xfce'
            'kwallet: support for storing passwords in KWallet on Plasma'
            'upower: Battery Status API support')
options=('!lto') # Chromium adds its own flags for ThinLTO
source=(https://commondatastorage.googleapis.com/chromium-browser-official/chromium-$pkgver.tar.xz
        https://github.com/foutrelis/chromium-launcher/archive/v$_launcher_ver/chromium-launcher-$_launcher_ver.tar.gz
        p010-Zero-Copy-for-VA-API-Decoding-for-Vulkan.patch
        add-feature-to-allow-zero-copy-video-formats.patch
        compiler-rt-adjust-paths.patch
        increase-fortify-level.patch
        use-oauth2-client-switches-as-default.patch
        0001-widevine-support-for-arm.patch
        0002-Run-blink-bindings-generation-single-threaded.patch)
sha256sums=('281daed29a5cb546f6273130035d9980666d2232f356ad95fc06af3c90121bc2'
            '213e50f48b67feb4441078d50b0fd431df34323be15be97c55302d3fdac4483a'
            '40db59162df2b7a2c0387bd620802f15424f637c09ba305b674fc09410ab21d1'
            '713dab4f8c26790c0e4a4c5ce6a9269e90446df5370cc14214a01a363f7afe39'
            'b3de01b7df227478687d7517f61a777450dca765756002c80c4915f271e2d961'
            'd634d2ce1fc63da7ac41f432b1e84c59b7cceabf19d510848a7cff40c8025342'
            'a9b417b96daec33c9059065e15b3a92ae1bf4b59f89d353659b335d9e0379db6'
            '4246da8a2d0bd689b6c8a6ce3042e72ec72497ea64ad47ceb92334afa34a27f1'
            '123dbf9cc82085242ee4a9bdaf6354257ea8eba993fb45fcb7f407a176c29abd')

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
_mpp_commit='1494a7bfb3bd51a6355bbae4f8518b9bfebb6a0a'
_mpp_parent="https://github.com/JeffyCN/meta-rockchip/raw/${_mpp_commit}/dynamic-layers/recipes-browser/chromium/chromium_${_pkgver_short}/"
for _mpp_patch in "${_mpp_patches[@]}"; do
  source+=("mpp-${_mpp_patch}::${_mpp_parent}${_mpp_patch}")
done
# Local patches on top of the MPP patches
_mpp_arch_patches=(
  '0001-media-sandbox-always-lookup-libv4l2-at-usr-lib-libv4.patch'
  '0002-wayland-never-use-implicit-sync.patch'
)
for _mpp_patch in "${_mpp_arch_patches[@]}"; do
  source+=("mpp-arch-${_mpp_patch}")
done
sha256sums+=(
  # MPP patches
  '91b6759bd04676653ef5b8dcf4dfc2070bc0ef3782e460feefada817b75fd2b4'
  'a49884ea66c769c77e9cb025074a2942ce2dc0561ccc35107e39132374385afd'
  '4342fe55b9530376dd3fafed6abf19e792ccb0d69d17eb255d685284fb227dd0'
  'a83027d40b1f56886a35d5522051745bdca4b48f73e19f89a30afd6efdf3f7f2'
  'e6885f5fdeb83b4cb420126c6081f27ea41e35d8cc9553504eb4f1e19bb5e7d7'
  '214d18475994a7bda5ef7004c684192e957eb973d04da34b197e1f1977c47cd3'
  'f04d6c4a68e87021d17cda914b0faccc8d4f23f841e41a0665a54d22439e3cdb'
  '3eeb26fb6f4d76e307a1312e687b9d94328eaf2dcaae85aa26472459f97c13fc'
  '9dd62cd87b6d6899ff31ceb5b30d29f6d46b36c6ed1ef496c92a417c2575d0d6'
  '290e4dd4bf8983ebb0c1589fbe8d8ac17a5c930560d5f625609f472cf902c43b'
  '411f07ee2cc587f7e33a5250648edb49bb513db91a281b2e9f4e9628f2e25651'
  'f25f3f3e7ba912c399445ff23ea54adc9ad267169d0f53e336995da976ea695e'
  '9c4327eafc83dc601c7755f2a15ae375e24643feb96855da8cbaa0465b732a81'
  '19b714a5c904e43cb7168c71f3273ea097251d73ac021cce3c86cf3c87c04e09'
  'fb4b5d3c92061e4db8c8f6dd3c0cc838c55402b15eb557d849bf345296705a9e'
  '8ad88eb85b93fab6b0e9c4ae0eaf5a069f3a55c0855c839dcf20c2f33f088abd'
  # Local patches on top of the MPP patches
  'ca909939f0ed659238c796f2ffb109677d51c17ac765ac8a857344fa235c2033'
  '739d39dc48f806052c0e506999e4339d590809b4fc1b64f85e0cc96a15024897'
)

# Possible replacements are listed in build/linux/unbundle/replace_gn_files.py
# Keys are the names in the above script; values are the dependencies in Arch
declare -gA _system_libs=(
  [brotli]=brotli
  #[dav1d]=dav1d
  #[ffmpeg]=ffmpeg    # YouTube playback stopped working in Chromium 120
  [flac]=flac
  [fontconfig]=fontconfig
  [freetype]=freetype2
  [harfbuzz-ng]=harfbuzz
  [icu]=icu
  #[jsoncpp]=jsoncpp  # needs libstdc++
  #[libaom]=aom
  #[libavif]=libavif  # needs -DAVIF_ENABLE_EXPERIMENTAL_GAIN_MAP=ON
  [libdrm]=
  [libjpeg]=libjpeg-turbo
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
    third_party/libxml/chromium/*.cc

  # Use the --oauth2-client-id= and --oauth2-client-secret= switches for
  # setting GOOGLE_DEFAULT_CLIENT_ID and GOOGLE_DEFAULT_CLIENT_SECRET at
  # runtime -- this allows signing into Chromium without baked-in values
  patch -Np1 -i ../use-oauth2-client-switches-as-default.patch

  # Upstream fixes
  patch -Np1 -i ../p010-Zero-Copy-for-VA-API-Decoding-for-Vulkan.patch
  patch -Np1 -i ../add-feature-to-allow-zero-copy-video-formats.patch

  # Allow libclang_rt.builtins from compiler-rt >= 16 to be used
  patch -Np1 -i ../compiler-rt-adjust-paths.patch

  # Increase _FORTIFY_SOURCE level to match Arch's default flags
  patch -Np1 -i ../increase-fortify-level.patch

  # MPP Patches
  for _mpp_patch in ${_mpp_patches[@]}; do
    patch -Np1 -i ../mpp-${_mpp_patch}
  done

  # MPP Patches
  for _mpp_arch_patch in ${_mpp_arch_patches[@]}; do
    patch -Np1 -i ../mpp-arch-${_mpp_arch_patch}
  done

  # Link to system tools required by the build
  rm third_party/node/linux/node-linux-x64/bin/node
  ln -s /usr/bin/node third_party/node/linux/node-linux-x64/bin/
  ln -s /usr/bin/java third_party/jdk/current/bin/

  # test deps are broken for ui/lens with system ICU
  # "//third_party/icu:icuuc_public" (taken from Gentoo ebuild)
  sed -i '/source_set("unit_tests") {/,/}/d' chrome/browser/ui/lens/BUILD.gn
  sed -i '/lens:unit_tests/d' chrome/test/BUILD.gn components/BUILD.gn

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
      'rust_bindgen_root="/usr"'
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
