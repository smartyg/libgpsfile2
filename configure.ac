#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.

AC_PREREQ([2.69])
AC_INIT([libgpsfile2], [0.0.1], [goedhart.martijn@gmail.com])
AC_CONFIG_SRCDIR([src/GpsfilePlugin.cpp])
AC_CONFIG_HEADERS([config.h])
AH_BOTTOM([#include <features.h>])

AC_CANONICAL_BUILD
AC_CONFIG_MACRO_DIR([m4])
AM_INIT_AUTOMAKE([foreign dist-xz])

# AX_DEFINE_SUBST(NAME, VALUE, DESCRIPTION)
# -----------------------------------------
AC_DEFUN([AX_DEFINE_SUBST], [
	AC_DEFINE([$1], ["$2"], [$3])
	AC_SUBST([$1], ['$2'])
])

# AX_CHECK_LIB(NAME, FUNC, NAME2)
# -----------------------------------------
AC_DEFUN([AX_CHECK_LIB], [
	AC_CHECK_LIB([$1], [$2], [AC_SUBST([$3_LIBS], ["-l$1"])
	AC_DEFINE([HAVE_LIB$3], [1], [Define if you have lib$1])], [], [$4])
])

AC_DEFUN([AX_CPP_CHECK_LIB], [
	AC_MSG_CHECKING([for $1])
	AC_LANG(C++)
	SAVED_LDFLAGS=$LDFLAGS
	LDFLAGS="$LDFLAGS -l$1 $5"
	AC_LINK_IFELSE(
	[AC_LANG_PROGRAM([#include <$3>],
		[$2])],
	[TEST_LIBS="$TEST_LIBS -l$1"] [HAVE_LIB$4=1],
	[])
	LDFLAGS=$SAVED_LDFLAGS
	if test "x$HAVE_LIB$4" == "x1"; then
		AC_MSG_RESULT([yes])
		AC_DEFINE([HAVE_LIB$4], [1], [Define if you have lib$1])
		AC_SUBST([$4_LIBS], ['-l$1'])
		AC_SUBST([$4_CFLAGS], [''])
	else
		AC_MSG_WARN([lib$1 is not installed.])
	fi
])

# some other package constants
AX_DEFINE_SUBST([PROGRAM_NAME], [Library gps file handler], [program name])
AX_DEFINE_SUBST([PROGRAM_SUMMARY], [Library to handle reading and writing of gps file], [program summary])
AX_DEFINE_SUBST([PROGRAM_DESCRIPTION], [...], [program description])
AX_DEFINE_SUBST([LAST_CHANGE], [23-07-2022], [Date of last change])
AX_DEFINE_SUBST([AUTHOR], [Martijn Goedhart], [Author])
AX_DEFINE_SUBST([COPYRIGHT], [Copyright (C) 2022 Martijn Goedhart], [Copyright])
AX_DEFINE_SUBST([LICENSE], [GNU General Public License], [License name])
AX_DEFINE_SUBST([LICENSE_SHORT], [GPLv3+], [License shorthand])
AX_DEFINE_SUBST([LICENSE_SHORT_RPM], [GPL-3.0+], [SPDX License code])
AX_DEFINE_SUBST([LICENSE_LONG], [GNU GPL version 3 or later <http://www.gnu.org/licenses/gpl-3.0.html>], [Long license description])
AX_DEFINE_SUBST([PACKAGE_HOMEPAGE], [http://www.martijn-goedhart.nl/sad/], [package home page])
AX_DEFINE_SUBST([LIBRARY_CURRENT_VERSION], [0], [version of this library])
AX_DEFINE_SUBST([LIBRARY_REVISION_VERSION], [0], [version of this library])
AX_DEFINE_SUBST([LIBRARY_AGE_VERSION], [0], [version of this library])

# Checks for programs.
AC_PROG_CXX
AC_PROG_CC
AC_PROG_AWK
AC_PROG_CPP
AC_PROG_INSTALL
AC_PROG_LN_S
AC_PROG_MAKE_SET

# this allows us specify individual liking flags for each target
AM_PROG_CC_C_O

# Initialize Libtool
LT_INIT

# Check if Libtool is present
# Libtool is used for building share libraries
AC_PROG_LIBTOOL

# Check which option are enabled and disabled
# use debuging
AC_ARG_ENABLE([debug],
	[AS_HELP_STRING([--enable-debug], [Turn on debugging @<:@default=disabled@:>@])],
	[case "${enableval}" in
		yes) debug=true ;;
		no)  debug=false ;;
		*) AC_MSG_ERROR([bad value ${enableval} for --enable-debug]) ;;
	esac],
	[debug=false])

# release flag (remove all debug code from compilation)
AC_ARG_ENABLE([release],
	[AS_HELP_STRING([--enable-release], [Compile for release (remove debug code) @<:@default=disabled@:>@])],
	[case "${enableval}" in
		yes) release=true; debug=no ;;
		no)  release=false ;;
		*) AC_MSG_ERROR([bad value ${enableval} for --enable-release]) ;;
	esac],
	[release=false])

# release flag (remove all debug code from compilation)
AC_ARG_ENABLE([gtest],
	[AS_HELP_STRING([--enable-gtest], [Enable googletest (gtest and gmock) for code testing @<:@default=disabled@:>@])],
	[case "${enableval}" in
		yes) gtest=true ;;
		no)  gtest=false ;;
		*) AC_MSG_ERROR([bad value ${enableval} for --enable-gtest]) ;;
	esac],
	[gtest=false])

# Check for GNU C++17 standard
AX_CXX_COMPILE_STDCXX_11(ext, mandatory)
AX_CXX_COMPILE_STDCXX_14(ext, mandatory)
AX_CXX_COMPILE_STDCXX_17(ext, mandatory)

# Define large file symbol
AC_SYS_LARGEFILE

# Checks for header files.
AC_CHECK_HEADERS([fcntl.h string.h sys/stat.h locale.h time.h])

# Checks for typedefs, structures, and compiler characteristics.
AC_CHECK_HEADER_STDBOOL
AC_TYPE_UID_T
AC_C_INLINE
AC_TYPE_MODE_T
AC_TYPE_OFF_T
AC_TYPE_SIZE_T
AC_TYPE_UINT64_T

# Checks for library functions.
AC_FUNC_MALLOC
AC_CHECK_FUNCS([memset setlocale strcpy])

# Checks for programs.
AC_CHECK_PROGS([GIT], [git])

# Checks for packages.
PKG_CHECK_MODULES([GPX], [gpsdata-utils])
PKG_CHECK_MODULES([GPX], [gpxlib])

# Checks for libraries.
AX_CHECK_LIB([dl], [dlopen], [DL])

if test "x$gtest" == xtrue; then
	PKG_CHECK_MODULES([GTEST], [gtest_main])
	PKG_CHECK_MODULES([GMOCK], [gmock_main])
fi

# Compute the major version of the library which is the last part of the library SONAME.
AC_SUBST([LIBRARY_OLD_INTERFACE], [$((LIBRARY_CURRENT_VERSION-LIBRARY_AGE_VERSION))])

if test "x$release" == xtrue; then
	AC_DEFINE([_RELEASE], [], [release flag])
	AM_SILENT_RULES([yes])
elif test "x$debug" == xtrue; then
	AC_DEFINE([_DEBUG], [], [debug flag])
else
	AM_SILENT_RULES([yes])
fi

if test -f libgpsfile2.spec.in
then
	AC_CONFIG_FILES([libgpsfile2.spec])
	git_version=$VERSION
	if test -n "$GIT"
	then
		git_version=$($GIT describe --tags --match='v@<:@0-9@:>@*' --dirty=-modified | $SED -E -e 's/^v(@<:@0-9@:>@@<:@.0-9@:>@*)(-(@<:@0-9@:>@+)-(@<:@0-9a-g@:>@+))?(-(modified))?/\1+git\3.\4.\6/' -e 's/@<:@.@:>@{2,}/./' -e 's/(\+git)?@<:@.@:>@$//')
		if test $? -ne 0
		then
			git_version=$VERSION
		fi
	fi
	AC_SUBST([GIT_VERSION], [$git_version])
fi

if test "x$gtest" == xtrue; then
	AC_CONFIG_FILES([tests/Makefile])
fi

AM_CONDITIONAL([DEBUG], [test x$debug = xtrue])
AM_CONDITIONAL([RELEASE], [test x$release = xtrue])
AM_CONDITIONAL([HAVE_GIT], [test -n "$GIT"])

AM_CONDITIONAL([GTEST], [test x$gtest = xtrue])
AM_CONDITIONAL([GMOCK], [test x$gtest = xtrue])

AC_CONFIG_FILES([Makefile libgpsfile2.pc])
AC_OUTPUT
