# $OpenBSD: Makefile,v 1.12 2012/05/03 06:35:31 ajacoutot Exp $

ONLY_FOR_ARCHS =	${GCC4_ARCHS}
ONLY_FOR_ARCHS-java =	i386 amd64
SHARED_ONLY =		Yes
VMEM_WARNING =		Yes

CATEGORIES =		graphics devel
COMMENT-docs =		OpenCV documentation and examples
COMMENT-main =		library of programming functions for real time cv
COMMENT-java =		Java bindings for OpenCV

V =			2.4.6.1
DISTNAME =		opencv-$V
PKGNAME-main =		${PKGNAME}
PKGNAME-docs =		opencv-docs-$V
PKGNAME-java =		opencv-java-$V

HOMEPAGE =		http://www.opencv.org/
MAINTAINER =		Rafael Sadowski <rafael@sizeofvoid.org>

SHARED_LIBS =		opencv_calib3d			1.0 #1.0
SHARED_LIBS +=		opencv_contrib			1.0 #1.0
SHARED_LIBS +=		opencv_core			1.0 #1.0
SHARED_LIBS +=		opencv_features2d		1.0 #1.0
SHARED_LIBS +=		opencv_flann			1.0 #1.0
SHARED_LIBS +=		opencv_gpu			1.0 #1.0
SHARED_LIBS +=		opencv_highgui			1.0 #1.0
SHARED_LIBS +=		opencv_imgproc			1.0 #1.0
SHARED_LIBS +=		opencv_legacy			1.0 #1.0
SHARED_LIBS +=		opencv_ml			1.0 #1.0
SHARED_LIBS +=		opencv_nonfree			0.0 #0.0
SHARED_LIBS +=		opencv_objdetect		1.0 #1.0
SHARED_LIBS +=		opencv_photo			0.0 #0.0
SHARED_LIBS +=		opencv_stitching		0.0 #0.0
SHARED_LIBS +=		opencv_superres			0.0 #0.0
SHARED_LIBS +=		opencv_ts			0.0 #0.0
SHARED_LIBS +=		opencv_video			1.0 #1.0
SHARED_LIBS +=		opencv_videostab		0.0 #0.0

MASTER_SITES =		${MASTER_SITE_SOURCEFORGE:=opencvlibrary/}

WANTLIB-main =  Half Iex IlmImf IlmThread Imath X11 Xcomposite Xcursor
WANTLIB-main += Xdamage Xext Xfixes Xi Xinerama Xrandr Xrender atk-1.0
WANTLIB-main += avcodec avformat avutil bz2 c cairo fontconfig
WANTLIB-main += freetype gdk-x11-2.0 gdk_pixbuf-2.0 gio-2.0 glib-2.0
WANTLIB-main += gmodule-2.0 gobject-2.0 gstapp-0.10 gstbase-0.10
WANTLIB-main += gstreamer-0.10 gstvideo-0.10 gthread-2.0 gtk-x11-2.0
WANTLIB-main += jasper jpeg m pango-1.0 pangocairo-1.0
WANTLIB-main += pangoft2-1.0 png pthread stdc++ swscale tiff xml2 z
WANTLIB-main += ${MODGETTEXT_WANTLIB} ${MODPY_WANTLIB}
WANTLIB-docs =
WANTLIB-java =  m pthread stdc++ z
WANTLIB-java += opencv_calib3d opencv_contrib opencv_core
WANTLIB-java += opencv_features2d opencv_flann opencv_highgui
WANTLIB-java += opencv_imgproc opencv_ml opencv_objdetect
WANTLIB-java += opencv_photo opencv_video

MULTI_PACKAGES =	-main -docs -java
PSEUDO_FLAVORS =	no_docs no_java
FLAVORS =		nonfree
FLAVOR ?=

# "nonfree" means containing some patented algorithms, see
# http://docs.opencv.org/modules/nonfree/doc/nonfree.html
# Please always build and test this FLAVOR during version updates.
# As some source modules include this functionality, we couldn't
# just subpackage "nonfree". Excluding this, here we have a usual
# BSDL
PERMIT_DISTFILES_FTP =		Yes
.if ${FLAVOR:Mnonfree}
PERMIT_PACKAGE_FTP =		No
PERMIT_PACKAGE_CDROM =		No
PERMIT_PACKAGE_FTP-docs =	Yes
PERMIT_PACKAGE_CDROM-docs =	Yes
.else
PERMIT_PACKAGE_CDROM =		Yes
.endif

.include <bsd.port.arch.mk>

MODULES =		devel/cmake \
			devel/gettext \
			lang/python

BUILD_DEPENDS =		devel/doxygen \
			math/py-numpy

RUN_DEPENDS-main =	${RUN_DEPENDS} \
			math/py-numpy
RUN_DEPENDS-docs =	${BUILD_PKGPATH},-main
RUN_DEPENDS-java =	${MODJAVA_RUN_DEPENDS}

LIB_DEPENDS-main =	${LIB_DEPENDS} \
			${MODPY_LIB_DEPENDS} \
			multimedia/gstreamer-0.10/plugins-base \
			x11/gtk+2 \
			graphics/ffmpeg \
			graphics/ilmbase \
			graphics/jpeg \
			graphics/openexr \
			graphics/png \
			graphics/tiff
LIB_DEPENDS-docs =
LIB_DEPENDS-java =	${BUILD_PKGPATH},-main

# ATM opencv requires V4L1 compat, ask them to change it,
# then see WITH_V4L below.
CONFIGURE_ARGS =	-DWITH_1394:Bool=Off \
			-DWITH_CUDA:Bool=Off \
			-DWITH_FFMPEG:Bool=On \
			-DWITH_GSTREAMER:Bool=On \
			-DWITH_GTK:Bool=On \
			-DWITH_JPEG:Bool=On \
			-DWITH_PNG:Bool=On \
			-DWITH_TIFF:Bool=On \
			-DWITH_V4L:Bool=Off

.if ${BUILD_PACKAGES:M-java}
MODULES +=		java
MODJAVA_VER =		1.6+
.else
# Safe: Java will be detected, if present, but won't be used
CONFIGURE_ARGS +=	-DBUILD_opencv_java:Bool=Off
.endif

.if ${BUILD_PACKAGES:M-docs}
BUILD_DEPENDS +=	textproc/py-sphinx
ALL_TARGET =		all html_docs
CONFIGURE_ARGS +=	-DBUILD_DOCS:Bool=On \
			-DBUILD_EXAMPLES:Bool=On \
			-DINSTALL_C_EXAMPLES:Bool=On \
			-DINSTALL_PYTHON_EXAMPLES:Bool=On
.else
CONFIGURE_ARGS +=	-DBUILD_DOCS:Bool=Off \
			-DBUILD_EXAMPLES:Bool=Off \
			-DINSTALL_PYTHON_EXAMPLES:Bool=Off
.endif

.if !${FLAVOR:Mnonfree}
CONFIGURE_ARGS +=	-DBUILD_opencv_nonfree:Bool=Off
.endif

SEPARATE_BUILD =	flavored

CONFIG_ADJ_CMD =	perl -pi
.for _l _v in ${SHARED_LIBS}
CONFIG_ADJ_CMD +=	-e 's,lib${_l}.so,lib${_l}.so.${_v},g;'
.endfor

DOCDIR =		${PREFIX}/share/doc/OpenCV

post-extract:
	cp ${FILESDIR}/how_to_scan_images.cpp \
		${WRKSRC}/samples/cpp/tutorial_code/core/how_to_scan_images/how_to_scan_images.cpp

post-configure:
	find ${WRKSRC} -type f -iname "*.make" -exec \
		perl -pi -e 's/(CXX_DEFINES =).*/$1/' {} \;

post-install:
	${CONFIG_ADJ_CMD} ${PREFIX}/share/OpenCV/OpenCVConfig.cmake
.if ${BUILD_PACKAGES:M-docs}
	rm -Rf ${DOCDIR}
	mv ${PREFIX}/share/OpenCV/doc ${DOCDIR}
	cp -R ${WRKBUILD}/doc/_html ${DOCDIR}/html
.endif

.include <bsd.port.mk>
