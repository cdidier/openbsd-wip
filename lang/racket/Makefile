# $OpenBSD$

SHARED_ONLY =		Yes
# Racket only supports i386 and amd64 on OpenBSD
ONLY_FOR_ARCHS =	i386 amd64

# XXX Change COMMENT and DESCR for racket 6. The core will not include
# the IDE, gui libs, teaching collects, etc. And probably "descendant of
# scheme" isn't the best description for racket.
COMMENT =		descendant of scheme with IDE and teaching resources

V =			5.3.6
PKGNAME =		racket-$V
# The project uses racket-* for stable and plt-* for development releases
DISTNAME =		racket-$V-src-unix
CATEGORIES =		lang
DPB_PROPERTIES =	parallel
HOMEPAGE =		http://racket-lang.org/
MAINTAINER =		Juan Francisco Cantero Hurtado <iam@juanfra.info>

# LGPLv3
PERMIT_PACKAGE_CDROM =	Yes

MASTER_SITES =		https://download.racket-lang.org/installers/${V}/racket/ \
			https://www.cs.utah.edu/plt/installers/${V}/racket/ \
			https://www.eecs.northwestern.edu/racket/${V}/racket/ \
			http://mirror.csclub.uwaterloo.ca/racket/racket-installers/${V}/racket/ \
			http://mirror.informatik.uni-tuebingen.de/mirror/racket/${V}/racket/ \
			http://racket.infogroep.be/${V}/racket/ \
			http://pre.racket-lang.org/release/installers/ \
			http://pre.racket-lang.org/installers/ \
			http://download.tuxfamily.org/jod/lang/racket/ \
			ftp://download.tuxfamily.org/jod/lang/racket/
EXTRACT_SUFX =		.tgz

MODULES +=		converters/libiconv

# http://docs.racket-lang.org/draw/libs.html
# http://docs.racket-lang.org/gui/libs.html
# Some deps are listed in the README, others in the doc and others
# are called by ffi.
LIB_DEPENDS =		devel/libffi>=3.0.9p2
BUILD_DEPENDS =		devel/pango \
			graphics/jpeg
RUN_DEPENDS =		x11/gtkglext \
			devel/libunique \
			databases/iodbc,-main

WANTLIB += c m pthread ffi

WRKDIST =		${WRKDIR}/racket-$V
WRKSRC =		${WRKDIST}/src

NO_TEST =		Yes
VMEM_WARNING =		Yes
SEPARATE_BUILD =	simple

CONFIGURE_STYLE =	gnu
CONFIGURE_ARGS +=	${CONFIGURE_SHARED} \
			--enable-libffi \
			--enable-gracket \
			--enable-jit \
			--enable-foreign \
			--enable-float \
			--enable-docs \
			--enable-pthread \
			--enable-lt=${LIBTOOL}
# "backtrace" is expensive.
# http://article.gmane.org/gmane.comp.lang.racket.devel/6700
# "places" and "futures" require thread-local storage.
# http://article.gmane.org/gmane.comp.lang.racket.user/16723
CONFIGURE_ARGS +=	--disable-backtrace \
			--disable-places \
			--disable-futures

# Since 5.3.4 if __SSE_MATH__ is defined (C_COMPILER_USES_SSE) and
# MZ_TRY_EXTFLONUMS enabled, MZ_LONG_DOUBLE (extflonum) is turned on. Also
# C_COMPILER_USES_SSE turn on MZ_USE_JIT_SSE.
# "-msse -mfpmath=sse" required by extflonum on i386.
CONFIGURE_ENV =		LDFLAGS="-L${LOCALBASE}/lib -L${X11BASE}/lib" \
			CPPFLAGS="-I${LOCALBASE}/include -I${X11BASE}/include" \
			CFLAGS="${CFLAGS} -msse -mfpmath=sse"

# MAKE_JOBS: If "places" is enabled, racket uses "places channels". When
# "places" is disabled, racket uses multiple racket processes and pipes.
# http://permalink.gmane.org/gmane.comp.lang.racket.user/17281
# https://github.com/plt/racket/commit/2295d16074b9538ce500cf9bf9de65265ce5531c
# Default limit in file descriptors isn't enough for compile as root. The
# "rendering" phase of scribble reaches the limit in some big documents.
do-install:
	@cd ${WRKBUILD} && ulimit -n 512 && ${SETENV} ${MAKE_ENV} \
		PLT_SETUP_OPTIONS="-j ${MAKE_JOBS}" ${MAKE_PROGRAM} \
		${ALL_FAKE_FLAGS} -f ${MAKE_FILE} ${FAKE_TARGET}

# Something in the ports framework or the racket installation modify the
# timestamp of two files. When raco sees a newer file than make-search.rkt,
# recompiles again the files but raco fails because the user doesn't have write
# permission in /usr. The easiest fix is to change the timestamp after "fake".
# This bug affects to "raco pkg install -u" and "raco pkg install -s". It will
# be fixed in the next version.
# A simple test: raco pkg install -u github://github.com/takikawa/dummy-pkg/master
post-fake:
	@cd ${WRKINST}/${PREFIX}/lib/racket/collects/scribblings/main/private && \
		touch -t 201306180906 search-context.html && \
		touch -t 201306180906 search.js

.if ${MACHINE_ARCH} == "amd64"
PKG_ARGS+=-Damd64=1 -Di386=0
.elif ${MACHINE_ARCH} == "i386"
PKG_ARGS+=-Damd64=0 -Di386=1
.endif

.include <bsd.port.mk>
