# $Id: PKGBUILD 266135 2016-04-24 17:36:21Z bpiotrowski $
# Maintainer: Ray Rashif <schiv@archlinux.org>
# Contributor: Tobias Powalowski <tpowa@archlinux.org>

pkgbase=opencv
pkgname=('opencv-gstreamer-rpitwo' 'opencv-gstreamer-rpitwo-samples')
pkgver=3.1.0
pkgrel=3
pkgdesc="Open Source Computer Vision Library"
arch=('armv7h')
license=('BSD')
url="http://opencv.org/"
depends=('openexr' 'xine-lib' 'libdc1394' 'gtkglext' 'gstreamer' 'gst-libav' 'ffmpeg' 'libx264' 'x264' 'gdal' 'libgphoto2')
makedepends=('cmake' 'python-numpy' 'python2-numpy' 'mesa' 'eigen')
optdepends=('opencv-samples'
            'eigen'
            'libcl: For coding with OpenCL'
            'python-numpy: Python 3 interface'
            'python2-numpy: Python 2 interface')
source=("$pkgbase-$pkgver.tar.gz::https://github.com/Itseez/opencv/archive/$pkgver.zip"
        "opencv_contrib-$pkgver.tar.gz::https://github.com/Itseez/opencv_contrib/archive/$pkgver.tar.gz"
        '5852.patch'
        'opencv_a0fdc91a14f07de25d858037940fcd3ba859b4e2.patch')
md5sums=('6082ee2124d4066581a7386972bfd52a'
         'a822839ad3ab79ff837c16785ea9dd10'
         '5bd9cd736b171c15cedee3a32a0c47ff'
         'aebe7878a572a2dc26a434bf08b8d851')

_cmakeopts=('-D WITH_OPENGL=ON'
            '-D WITH_TBB=ON'
            '-D WITH_V4L=ON'
            '-D WITH_QT=OFF'
            '-D WITH_GDAL=ON'
            '-D BUILD_NEW_PYTHON_SUPPORT=ON'
            '-D ENABLE_FAST_MATH=1'
            '-D WITH_XINE=ON'
            '-D WITH_GSTREAMER=ON'
            '-D BUILD_WITH_DEBUG_INFO=OFF'
            '-D BUILD_TESTS=OFF'
            '-D BUILD_PERF_TESTS=OFF'
            '-D BUILD_EXAMPLES=ON'
            '-D INSTALL_C_EXAMPLES=ON'
            '-D INSTALL_PYTHON_EXAMPLES=ON'
            '-D CMAKE_BUILD_TYPE=Release'
            '-D CMAKE_INSTALL_PREFIX=/usr'
            '-D CMAKE_SKIP_RPATH=ON'
            '-D ENABLE_VFPV3=ON'
            '-D ENABLE_NEON=ON'
            #'-D WITH_IPP=ON'             '-D FORCE_VTK=ON'
            #'-D INSTALL_CREATE_DISTRIB=ON'
            )

# SSE only available from Pentium 3 onwards (i686 is way older)
[[ "$CARCH" = 'i686' ]] && \
  _cmakeopts+=('-D ENABLE_SSE=OFF'
               '-D ENABLE_SSE2=OFF'
               '-D ENABLE_SSE3=OFF')

prepare() {
  cd "$srcdir/"$(echo $pkgname | sed -e s/-gstreamer-rpitwo//)"-$pkgver"
  patch -p1 -i "$srcdir/5852.patch"
  # Patch gcc 6.1.1 -isystem compilation problem with c std headers, see https://github.com/Itseez/opencv/issues/6517
  patch -p1 -i "$srcdir/opencv_a0fdc91a14f07de25d858037940fcd3ba859b4e2.patch"
  # Vtk 7 need use of vtkRenderingOpenGL2 instead of vtkRenderingOpenGL
  sed -i -e "s/\(vtkRenderingOpenGL\)/\12/g" "$srcdir/"$(echo $pkgname | sed -e s/-gstreamer-rpitwo//)"-$pkgver/cmake/OpenCVDetectVTK.cmake"
}

build() {
  cd "$srcdir/"$(echo $pkgname | sed -e s/-gstreamer-rpitwo//)"-$pkgver"

  cmake ${_cmakeopts[@]} \
    -DOPENCV_EXTRA_MODULES_PATH="$srcdir/opencv_contrib-$pkgver/modules" \
    .

  make
}

package_opencv-gstreamer-rpitwo() {
  provides=('opencv')
  conflicts=('opencv')
  options=('staticlibs')

  cd "$srcdir/"$(echo $pkgname | sed -e s/-gstreamer-rpitwo//)"-$pkgver"

  make DESTDIR="$pkgdir" install

  # install license file
  install -Dm644 "$srcdir/"$(echo $pkgname | sed -e s/-gstreamer-rpitwo//)"-$pkgver/LICENSE" \
    "$pkgdir/usr/share/licenses/"$(echo $pkgname | sed -e s/-gstreamer-rpitwo//)"/LICENSE"

  cd "$pkgdir/usr/share"

  # separate samples package; also be -R friendly
  if [[ -d OpenCV/samples ]]; then
    mv OpenCV/samples "$srcdir/"$(echo $pkgname | sed -e s/-gstreamer-rpitwo//)"-samples"
    mv OpenCV "$(echo $pkgname | sed -e s/-gstreamer-rpitwo//)" # otherwise folder naming is inconsistent
  elif [[ ! -d OpenCV ]]; then
    warning "Directory naming issue; samples package may not be built!"
  fi
}

package_opencv-gstreamer-rpitwo-samples() {
  provides=('opencv-samples')
  conflicts=('opencv-samples')
  pkgdesc+=" (samples)"
  depends=("opencv-gstreamer=$pkgver") # sample codes change with lib/API
  unset optdepends

  mkdir -p "$pkgdir/usr/share/opencv"
  cp -r "$srcdir/opencv-samples" "$pkgdir/usr/share/opencv/samples"

  # install license file
  install -Dm644 "$srcdir/opencv-$pkgver/LICENSE" \
    "$pkgdir/usr/share/licenses/"$(echo $pkgname | sed -e s/-gstreamer-rpitwo//)"/LICENSE"
}

# vim:set ts=2 sw=2 et:
