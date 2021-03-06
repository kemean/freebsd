m4_define([maj_ver], [0])
m4_define([med_ver], [3])
m4_define([min_ver], [0])
m4_define([so_version], [maj_ver:med_ver])
m4_define([ucl_version], [maj_ver.med_ver.min_ver])

AC_INIT([libucl],[ucl_version],[https://github.com/vstakhov/libucl],[libucl])
AC_CONFIG_SRCDIR([configure.ac])
AM_INIT_AUTOMAKE([1.11 foreign silent-rules -Wall -Wportability no-dist-gzip dist-xz])

UCL_VERSION=ucl_version
SO_VERSION=so_version

AC_SUBST(UCL_VERSION)
AC_SUBST(SO_VERSION)

AC_PROG_CC_C99
AM_PROG_CC_C_O
AM_PROG_AR
LT_INIT
AC_CONFIG_MACRO_DIR([m4])
AC_CONFIG_HEADERS([config.h])

AC_C_CONST
AC_TYPE_SIZE_T

AC_CHECK_HEADERS_ONCE([fcntl.h unistd.h])
AC_TYPE_OFF_T
AC_FUNC_MMAP
AC_CHECK_HEADERS_ONCE([fcntl.h])
AC_CHECK_HEADERS_ONCE([sys/types.h])
AC_CHECK_HEADERS_ONCE([sys/stat.h])
AC_CHECK_HEADERS_ONCE([sys/param.h])
AC_CHECK_HEADERS_ONCE([sys/mman.h])
AC_CHECK_HEADERS_ONCE([stdlib.h])
AC_CHECK_HEADERS_ONCE([stddef.h])
AC_CHECK_HEADERS_ONCE([stdarg.h])
AC_CHECK_HEADERS_ONCE([stdbool.h])
AC_CHECK_HEADERS_ONCE([stdint.h])
AC_CHECK_HEADERS_ONCE([string.h])
AC_CHECK_HEADERS_ONCE([unistd.h])
AC_CHECK_HEADERS_ONCE([ctype.h])
AC_CHECK_HEADERS_ONCE([errno.h])
AC_CHECK_HEADERS_ONCE([limits.h])
AC_CHECK_HEADERS_ONCE([libgen.h])
AC_CHECK_HEADERS_ONCE([stdio.h])
AC_CHECK_HEADERS_ONCE([float.h])
AC_CHECK_HEADERS_ONCE([math.h])

dnl Example of default-disabled feature
AC_ARG_ENABLE([urls], AS_HELP_STRING([--enable-urls], [Enable URLs fetch (requires libfetch or libcurl)]))
AC_ARG_ENABLE([signatures], AS_HELP_STRING([--enable-signatures],
	[Enable signatures check (requires openssl)]))
AC_ARG_ENABLE([utils],
	[--enable-utils Build and install utils],
	[case "${enableval}" in
  		yes) utils=true ;;
  		no)  utils=false ;;
  		*) AC_MSG_ERROR([bad value ${enableval} for --enable-utils]) ;;
	esac],[utils=false])
AM_CONDITIONAL([UTILS], [test x$utils = xtrue])

AS_IF([test "x$enable_signatures" = "xyes"], [
	AC_SEARCH_LIBS([EVP_MD_CTX_create], [crypto], [
		AC_DEFINE(HAVE_OPENSSL, 1, [Define to 1 if you have the 'crypto' library (-lcrypto).])
		LIBSSL_LIB="-lcrypto"
		LIBS_EXTRA="${LIBS_EXTRA} -lcrypto"
		], [AC_MSG_ERROR([unable to find the EVP_MD_CTX_create() function])])
])

AC_PATH_PROG(PANDOC, pandoc, [/non/existent])

AC_SEARCH_LIBS([clock_gettime], [rt], [], [
	AC_CHECK_HEADER([mach/mach_time.h], [
		AC_DEFINE(HAVE_MACH_MACH_TIME_H, 1, [Define to 1 on Darwin])
	], [AC_MSG_ERROR([unable to find clock_gettime or mach_absolute_time])])
])
AC_SEARCH_LIBS([remainder], [m], [], [AC_MSG_ERROR([unable to find remainder() function])])

AC_CHECK_HEADER([regex.h], [
	AC_DEFINE(HAVE_REGEX_H, 1, [Define to 1 if you have the <regex.h> header file.])
	],
	[AC_MSG_ERROR([unable to find the regex.h header])],
	[#include <sys/types.h>])

AS_IF([test "x$enable_urls" = "xyes"], [
	AC_CHECK_HEADER([fetch.h], [
		AC_DEFINE(HAVE_FETCH_H, 1, [Define to 1 if you have the <fetch.h> header file.])
		AC_CHECK_LIB(fetch, fetchXGet, [
			AC_DEFINE(HAVE_LIBFETCH, 1, [Define to 1 if you have the 'fetch' library (-lfetch).])
			LIBFETCH_LIBS="-lfetch"
			have_libfetch="yes"
			LIBS_EXTRA="${LIBS_EXTRA} -lfetch"
		])
	], [],[
	#include <stdio.h>
	#ifdef HAVE_SYS_PARAM_H
	#include <sys/param.h>
	#endif
	])
	AC_SUBST(LIBFETCH_LIBS)

	AS_IF([ test "x$have_libfetch" != "xyes"], [
		dnl Fallback to libcurl
		PKG_CHECK_MODULES([CURL], [libcurl], [
			AC_DEFINE(CURL_FOUND, 1, [Use libcurl])
			LIBS_EXTRA="${LIBS_EXTRA} -lcurl"],
		[AC_MSG_ERROR([unable to find neither libfetch nor libcurl])])
	])
	AC_SUBST(CURL_FOUND)
	AC_SUBST(CURL_LIBS)
	AC_SUBST(CURL_CFLAGS)
])

AC_SUBST(LIBS_EXTRA)

AC_CONFIG_FILES(Makefile \
	src/Makefile \
	tests/Makefile \
	utils/Makefile \
	doc/Makefile \
	libucl.pc)
AC_CONFIG_FILES([stamp-h], [echo timestamp > stamp-h])
AC_OUTPUT
