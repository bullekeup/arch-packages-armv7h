# $Id: PKGBUILD 266135 2016-04-24 17:36:21Z bpiotrowski $
# Maintainer: Ray Rashif <schiv@archlinux.org>
# Contributor: Tobias Powalowski <tpowa@archlinux.org>

pkgbase=opencv
pkgname=('opencv-gstreamer-rpitwo' 'opencv-gstreamer-rpitwo-samples')
pkgver=3.2.0
pkgrel=1
pkgdesc="Open Source Computer Vision Library"
arch=('armv7h')
license=('BSD')
url="http://opencv.org/"
depends=('openexr' 'xine-lib' 'libdc1394' 'gtkglext' 'gstreamer' 'gst-libav' 'ffmpeg' 'libx264' 'x264' 'gdal' 'libgphoto2' 'intel-tbb' 'hdf5-cpp-fortran' 'python-h5py' 'python2-h5py' 'protobuf' 'google-glog' 'doxygen' 'cpio' 'lzop' 'p7zip' 'unrar' 'unzip' 'zip')
makedepends=('cmake' 'python-numpy' 'python2-numpy' 'mesa' 'eigen' 'hdf5-cpp-fortran' 'python-h5py' 'python2-h5py' 'libclc')
optdepends=('opencv-samples'
            'eigen'
            'libcl: For coding with OpenCL'
            'python-numpy: Python 3 interface'
            'python2-numpy: Python 2 interface')
source=("$pkgbase-$pkgver.tar.gz::https://github.com/Itseez/opencv/archive/$pkgver.zip"
        "opencv_contrib-$pkgver.tar.gz::https://github.com/Itseez/opencv_contrib/archive/$pkgver.tar.gz")
md5sums=('bfc6a261eb069b709bcfe7e363ef5899'
         'd7d50c70c31df3b31310f548f31fd2a2')

_cmakeopts=('-D WITH_OPENCL=ON'
            '-D WITH_OPENGL=ON'
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
            '-D BUILD_opencv_python2=ON',
            '-D BUILD_opencv_python3=ON',
            '-D CMAKE_BUILD_TYPE=Release'
            '-D CMAKE_INSTALL_PREFIX=/usr'
            '-D CMAKE_SKIP_RPATH=ON'
            '-D ENABLE_VFPV3=ON'
            '-D ENABLE_NEON=ON'
            '-D BUILD_opencv_dnn=ON'
            '-D BUILD_LIBPROTOBUF_FROM_SOURCES=ON'
            '-D BUILD_opencv_face=ON'
            '-D BUILD_opencv_ml=ON'
            '-D ENABLE_SSE=OFF'
            '-D ENABLE_SSE2=OFF'
            '-D ENABLE_SSE3=OFF'
            '-D ENABLE_PRECOMPILED_HEADERS=OFF'
            #'-D BUILD_opencv_world=ON'
            #'-D BUILD_opencv_contrib_world=ON'
            #'-D BUILD_opencv_world=ON'
            #'-D WITH_CUDA=OFF'
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
}

build() {
  cd "$srcdir/"$(echo $pkgname | sed -e s/-gstreamer-rpitwo//)"-$pkgver"

  cmake ${_cmakeopts[@]} \
    -DOPENCV_EXTRA_MODULES_PATH="$srcdir/opencv_contrib-$pkgver/modules" \
    .

  make
}

package_opencv-gstreamer-rpitwo() {
  provides=('opencv' 'opencv-gstreamer')
  conflicts=('opencv' 'opencv-gstreamer')
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
  provides=('opencv-samples' 'opencv-gstreamer-samples')
  conflicts=('opencv-samples' 'opencv-gstreamer-samples')
  pkgdesc+=" (samples)"
  depends=("opencv-gstreamer-rpitwo=$pkgver") # sample codes change with lib/API
  unset optdepends

  mkdir -p "$pkgdir/usr/share/opencv"
  cp -r "$srcdir/opencv-samples" "$pkgdir/usr/share/opencv/samples"

  # install license file
  install -Dm644 "$srcdir/opencv-$pkgver/LICENSE" \
    "$pkgdir/usr/share/licenses/"$(echo $pkgname | sed -e s/-gstreamer-rpitwo//)"/LICENSE"
}

# vim:set ts=2 sw=2 et:
