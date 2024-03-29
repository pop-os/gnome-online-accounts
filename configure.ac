m4_define([goa_major_version], [3])
m4_define([goa_minor_version], [44])
m4_define([goa_micro_version], [0])
m4_define([goa_nano_version], [0])

m4_define([goa_base_version],
          [goa_major_version.goa_minor_version.goa_micro_version])
m4_define([goa_version],
          [m4_if(goa_nano_version, 0, [goa_base_version],
                 [goa_base_version].[goa_nano_version])])

AC_INIT([GNOME Online Accounts],
        [goa_version],
        [https://gitlab.gnome.org/GNOME/gnome-online-accounts/issues],
        [gnome-online-accounts],
        [https://wiki.gnome.org/Projects/GnomeOnlineAccounts])

AM_INIT_AUTOMAKE([1.9 foreign dist-xz no-dist-gzip tar-ustar])
AC_CONFIG_MACRO_DIR([m4])
AC_CONFIG_SRCDIR([src])
AC_CONFIG_HEADERS([config.h])

AM_MAINTAINER_MODE([enable])

AX_IS_RELEASE([git-directory])

AX_COMPILER_FLAGS([WARN_CFLAGS],
                  [WARN_LDFLAGS],
                  [$ax_is_release],
                  [],
                  [-Wno-cast-function-type -Wno-error=cast-function-type])

AC_PROG_CC
AC_PROG_LIBTOOL

PKG_PROG_PKG_CONFIG(0.16)

m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES([yes])])

GOA_MAJOR_VERSION=goa_major_version
GOA_MINOR_VERSION=goa_minor_version
GOA_MICRO_VERSION=goa_micro_version

AC_SUBST(GOA_MAJOR_VERSION)
AC_SUBST(GOA_MINOR_VERSION)
AC_SUBST(GOA_MICRO_VERSION)

# Before making a release, the LT_VERSION string should be modified.
# The string is of the form C:R:A.
# - If interfaces have been changed or added, but binary compatibility has
#   been preserved, change to C+1:0:A+1
# - If binary compatibility has been broken (eg removed or changed interfaces)
#   change to C+1:0:0
# - If the interface is the same as the previous version, change to C:R+1:A
GOA_LT_VERSION=0:0:0
GOA_BACKEND_LT_VERSION=1:0:0
AC_SUBST(GOA_LT_VERSION)
AC_SUBST(GOA_BACKEND_LT_VERSION)

AC_DEFINE(GOA_MAJOR_VERSION,
          [goa_major_version],
          [Define to the GOA major version])
AC_DEFINE(GOA_MINOR_VERSION,
          [goa_minor_version],
          [Define to the GOA minor version])
AC_DEFINE(GOA_MICRO_VERSION,
          [goa_micro_version],
          [Define to the GOA micro version])

# Initialization
#

AX_REQUIRE_DEFINED([GLIB_GSETTINGS])
GLIB_GSETTINGS

AC_ARG_ENABLE([documentation],
	AS_HELP_STRING([--enable-documentation], [enable man pages]),
	[], [enable_documentation=no])
AM_CONDITIONAL(ENABLE_DOCUMENTATION, test x$enable_documentation = xyes)
if test x$enable_documentation = xyes; then
   AC_PATH_PROG([XSLTPROC], [xsltproc])
   if test -z "$XSLTPROC"; then
     AC_MSG_ERROR([xsltproc is needed])
   fi
fi

AX_REQUIRE_DEFINED([GTK_DOC_CHECK])
GTK_DOC_CHECK([1.3])

# Libraries
#

PKG_CHECK_MODULES(GLIB, [glib-2.0 gio-2.0 gio-unix-2.0 >= 2.52])
AC_SUBST(GLIB_CFLAGS)
AC_SUBST(GLIB_LIBS)

AX_REQUIRE_DEFINED([GOBJECT_INTROSPECTION_CHECK])
GOBJECT_INTROSPECTION_CHECK([0.6.2])

VAPIGEN_CHECK()

AC_ARG_ENABLE([backend],
              [AS_HELP_STRING([--enable-backend],
              [Enable goabackend library])],
	      [],
	      [enable_backend=yes])
AM_CONDITIONAL(BUILD_BACKEND, [test x$enable_backend != xno])

if test "$enable_backend" != "no"; then
  AC_DEFINE(GOA_BACKEND_ENABLED, 1, [Enable goabackend library])

  PKG_CHECK_MODULES(GTK, [gtk+-3.0 >= 3.19.12])
  AC_SUBST(GTK_CFLAGS)
  AC_SUBST(GTK_LIBS)

  PKG_CHECK_MODULES(JAVASCRIPT_CORE_GTK, [javascriptcoregtk-4.0 >= 2.12.0])
  AC_SUBST(JAVASCRIPT_CORE_GTK_CFLAGS)
  AC_SUBST(JAVASCRIPT_CORE_GTK_LIBS)

  PKG_CHECK_MODULES(WEBKIT_GTK, [webkit2gtk-4.0 >= 2.26.0])
  AC_SUBST(WEBKIT_GTK_CFLAGS)
  AC_SUBST(WEBKIT_GTK_LIBS)

  PKG_CHECK_MODULES(LIBSOUP, [libsoup-2.4 >= 2.42])
  AC_SUBST(LIBSOUP_CFLAGS)
  AC_SUBST(LIBSOUP_LIBS)

  PKG_CHECK_MODULES(JSON_GLIB, [json-glib-1.0])
  AC_SUBST(JSON_GLIB_CFLAGS)
  AC_SUBST(JSON_GLIB_LIBS)

  PKG_CHECK_MODULES(REST, [rest-0.7])
  AC_SUBST(REST_CFLAGS)
  AC_SUBST(REST_LIBS)

  PKG_CHECK_MODULES(SECRET, [libsecret-1])
  AC_SUBST(SECRET_CFLAGS)
  AC_SUBST(SECRET_LIBS)

  PKG_CHECK_MODULES(LIBXML, [libxml-2.0])
  AC_SUBST(LIBXML_CFLAGS)
  AC_SUBST(LIBXML_LIBS)
fi

AC_ARG_ENABLE([inspector],
              [AS_HELP_STRING([--enable-inspector], [Enable a WebKitWebInspector for the embedded web view])],
              [],
              [enable_inspector=no])
if test "$enable_inspector" != "no"; then
  AC_DEFINE(GOA_INSPECTOR_ENABLED, 1, [Enable a WebKitWebInspector for the embedded web view])
fi

AC_ARG_WITH(template-file,
            [AS_HELP_STRING([--with-template-file], [Path to the template file])],
            [],
            [])
AC_DEFINE_UNQUOTED(GOA_TEMPLATE_FILE, ["$with_template_file"], [Path to the template file])

# service providers
#

# Microsoft Exchange
AC_DEFINE(GOA_EXCHANGE_NAME, ["exchange"], [ProviderType and extension point name])
AC_ARG_ENABLE([exchange],
              [AS_HELP_STRING([--enable-exchange], [Enable Microsoft Exchange provider])],
              [],
              [enable_exchange=yes])
if test "$enable_exchange" != "no"; then
  AC_DEFINE(GOA_EXCHANGE_ENABLED, 1, [Enable Microsoft Exchange data provider])
fi

# Fedora
AC_DEFINE(GOA_FEDORA_NAME, ["fedora"], [ProviderType and extension point name])
AC_DEFINE(GOA_FEDORA_REALM, ["FEDORAPROJECT.ORG"], [Kerberos realm used by the Fedora Account System])
AC_ARG_ENABLE([fedora],
              [AS_HELP_STRING([--enable-fedora], [Enable Fedora Account System support])],
              [enable_fedora=$enableval],
              [enable_fedora=auto])
AC_PATH_PROG([KRB5_CONFIG], krb5-config, none, $PATH:/usr/kerberos/bin)

if test "x$KRB5_CONFIG" != "xnone"; then
    KRB5_LIBS="`${KRB5_CONFIG} --libs krb5`"
    KRB5_CFLAGS="`${KRB5_CONFIG} --cflags krb5`"
    PKG_CHECK_MODULES(GCR, gcr-3, [have_fedora=yes], [have_fedora=no])
else
    KRB5_LIBS=""
    KRB5_CFLAGS=""
    have_fedora=no
fi
AC_SUBST(KRB5_CFLAGS)
AC_SUBST(KRB5_LIBS)

if test "$enable_fedora" != "no"; then
  if test "$enable_fedora" == "yes" &&
     test "$have_fedora" == "no" ; then
      AC_MSG_ERROR([Fedora Account System support requested, but not available])
  fi

  enable_fedora=$have_fedora

  if test "$enable_fedora" == "yes"; then
    AC_DEFINE(GCR_API_SUBJECT_TO_CHANGE, 1, [Define to use the GCR API])
    AC_DEFINE(GOA_FEDORA_ENABLED, 1, [Enable Fedora Account System provider])
  fi
else
  enable_fedora=no
fi
AM_CONDITIONAL(BUILD_FEDORA, [test x$enable_fedora != xno])

# Flickr
AC_DEFINE(GOA_FLICKR_NAME, ["flickr"], [ProviderType and extension point name])
AC_ARG_ENABLE([flickr],
              [AS_HELP_STRING([--enable-flickr], [Enable Flickr provider])],
              [],
              [enable_flickr=yes])
AC_ARG_WITH(flickr-consumer-key,
            [AS_HELP_STRING([--with-flickr-consumer-key], [Flickr OAuth 1.0 consumer key])],
            [],
            [])
AC_ARG_WITH(flickr-consumer-secret,
            [AS_HELP_STRING([--with-flickr-consumer-secret], [Flickr OAuth 1.0 consumer secret])],
            [],
            [])
if test "$with_flickr_consumer_key" = ""; then
  with_flickr_consumer_key=ed00ad7e0869897506e23c0d18e34d01
fi
if test "$with_flickr_consumer_secret" = ""; then
  with_flickr_consumer_secret=ebd556dd187188b1
fi
AC_DEFINE_UNQUOTED(GOA_FLICKR_CONSUMER_KEY, ["$with_flickr_consumer_key"], [Flickr OAuth 1.0 consumer key])
AC_DEFINE_UNQUOTED(GOA_FLICKR_CONSUMER_SECRET, ["$with_flickr_consumer_secret"], [Flickr OAuth 1.0 consumer secret])
if test "$enable_flickr" != "no"; then
  AC_DEFINE(GOA_FLICKR_ENABLED, 1, [Enable Flickr data provider])
fi

# Foursquare
AC_DEFINE(GOA_FOURSQUARE_NAME, ["foursquare"], [ProviderType and extension point name])
AC_ARG_ENABLE([foursquare],
              [AS_HELP_STRING([--enable-foursquare], [Enable Foursquare provider])],
              [],
              [enable_foursquare=yes])
AC_ARG_WITH(foursquare-client-id,
            [AS_HELP_STRING([--with-foursquare-client-id], [Foursquare OAuth 2.0 client id])],
            [],
            [])
if test "$with_foursquare_client_id" = ""; then
  with_foursquare_client_id="MBNU2NES5HASNDQJ25YPFGG2UGRZHPI3IYTNJGE0KIWT2HCF"
fi
AC_DEFINE_UNQUOTED(GOA_FOURSQUARE_CLIENT_ID, ["$with_foursquare_client_id"], [Foursquare OAuth 2.0 client id])
if test "$enable_foursquare" != "no"; then
  AC_DEFINE(GOA_FOURSQUARE_ENABLED, 1, [Enable Foursquare data provider])
fi

# Google
AC_DEFINE(GOA_GOOGLE_NAME, ["google"], [ProviderType and extension point name])
AC_ARG_ENABLE([google],
              [AS_HELP_STRING([--enable-google], [Enable Google provider])],
              [],
              [enable_google=yes])
AC_ARG_WITH(google-client-id,
            [AS_HELP_STRING([--with-google-client-id], [Google OAuth 2.0 client id])],
            [],
            [])
AC_ARG_WITH(google-client-secret,
            [AS_HELP_STRING([--with-google-client-secret], [Google OAuth 2.0 client secret])],
            [],
            [])
if test "$with_google_client_id" = ""; then
  with_google_client_id=44438659992-7kgjeitenc16ssihbtdjbgguch7ju55s.apps.googleusercontent.com
fi
if test "$with_google_client_secret" = ""; then
  with_google_client_secret=-gMLuQyDiI0XrQS_vx_mhuYF
fi
AC_DEFINE_UNQUOTED(GOA_GOOGLE_CLIENT_ID, ["$with_google_client_id"], [Google OAuth 2.0 client id])
AC_DEFINE_UNQUOTED(GOA_GOOGLE_CLIENT_SECRET, ["$with_google_client_secret"], [Google OAuth 2.0 client secret])
if test "$enable_google" != "no"; then
  AC_DEFINE(GOA_GOOGLE_ENABLED, 1, [Enable Google data provider])
fi

# IMAP/SMTP
AC_DEFINE(GOA_IMAP_SMTP_NAME, ["imap_smtp"], [ProviderType and extension point name])
AC_ARG_ENABLE([imap-smtp],
              [AS_HELP_STRING([--enable-imap-smtp], [Enable IMAP/SMTP provider])],
              [],
              [enable_imap_smtp=yes])
if test "$enable_imap_smtp" != "no"; then
  AC_DEFINE(GOA_IMAP_SMTP_ENABLED, 1, [Enable IMAP/SMTP data provider])
fi

# Media Server
AC_DEFINE(GOA_MEDIA_SERVER_NAME, ["media-server"], [ProviderType and extension point name])
AC_ARG_ENABLE([media-server],
              [AS_HELP_STRING([--enable-media-server], [Enable Media Server provider])],
              [],
              [enable_media_server=no])
if test "$enable_media_server" != "no"; then
  AC_DEFINE(GOA_MEDIA_SERVER_ENABLED, 1, [Enable Media Server provider])
fi

# ownCloud
AC_DEFINE(GOA_OWNCLOUD_NAME, ["owncloud"], [ProviderType and extension point name])
AC_ARG_ENABLE([owncloud],
              [AS_HELP_STRING([--enable-owncloud], [Enable ownCloud provider])],
              [],
              [enable_owncloud=yes])
if test "$enable_owncloud" != "no"; then
  AC_DEFINE(GOA_OWNCLOUD_ENABLED, 1, [Enable ownCloud data provider])
fi

# Facebook
AC_DEFINE(GOA_FACEBOOK_NAME, ["facebook"], [ProviderType and extension point name])
AC_ARG_ENABLE([facebook],
              [AS_HELP_STRING([--enable-facebook], [Enable Facebook provider])],
              [],
              [enable_facebook=yes])
AC_ARG_WITH(facebook-client-id,
            [AS_HELP_STRING([--with-facebook-client-id], [Facebook OAuth 2.0 client id])],
            [],
            [])
if test "$with_facebook_client_id" = ""; then
  with_facebook_client_id=297654143624603
fi
AC_DEFINE_UNQUOTED(GOA_FACEBOOK_CLIENT_ID, ["$with_facebook_client_id"], [Facebook OAuth 2.0 client id])
if test "$enable_facebook" != "no"; then
  AC_DEFINE(GOA_FACEBOOK_ENABLED, 1, [Enable Facebook data provider])
fi

# Windows Live
AC_DEFINE(GOA_WINDOWS_LIVE_NAME, ["windows_live"], [ProviderType and extension point name])
AC_ARG_ENABLE([windows-live],
              [AS_HELP_STRING([--enable-windows-live], [Enable Windows Live provider])],
              [],
              [enable_windows_live=yes])
AC_ARG_WITH(windows-live-client-id,
            [AS_HELP_STRING([--with-windows-live-client-id], [Windows Live OAuth 2.0 client id])],
            [],
            [])
if test "$with_windows_live_client_id" = ""; then
  with_windows_live_client_id=0000000044067703
fi
AC_DEFINE_UNQUOTED(GOA_WINDOWS_LIVE_CLIENT_ID, ["$with_windows_live_client_id"], [Windows Live OAuth 2.0 client id])
if test "$enable_windows_live" != "no"; then
  AC_DEFINE(GOA_WINDOWS_LIVE_ENABLED, 1, [Enable Windows Live data provider])
fi

# Kerberos
AC_DEFINE(GOA_KERBEROS_NAME, ["kerberos"], [ProviderType and extension point name])
AC_ARG_ENABLE([kerberos],
              [AS_HELP_STRING([--enable-kerberos], [Enable kerberos support])],
              [enable_kerberos=$enableval],
              [enable_kerberos=auto])
AC_PATH_PROG([KRB5_CONFIG], krb5-config, none, $PATH:/usr/kerberos/bin)

if test "x$KRB5_CONFIG" != "xnone"; then
    KRB5_LIBS="`${KRB5_CONFIG} --libs krb5`"
    KRB5_CFLAGS="`${KRB5_CONFIG} --cflags krb5`"
    PKG_CHECK_MODULES(GCR, gcr-3, [have_kerberos=yes], [have_kerberos=no])
else
    KRB5_LIBS=""
    KRB5_CFLAGS=""
    have_kerberos=no
fi
AC_SUBST(KRB5_CFLAGS)
AC_SUBST(KRB5_LIBS)

if test "$enable_kerberos" != "no"; then
  if test "$enable_kerberos" == "yes" &&
     test "$have_kerberos" == "no" ; then
      AC_MSG_ERROR([kerberos support requested, but not available])
  fi

  enable_kerberos=$have_kerberos

  if test "$enable_kerberos" == "yes"; then
    AC_DEFINE(GCR_API_SUBJECT_TO_CHANGE, 1, [Define to use the GCR API])
    AC_DEFINE(GOA_KERBEROS_ENABLED, 1, [Enable Enterprise Identity data provider])
  fi
else
  enable_kerberos=no
fi
AM_CONDITIONAL(BUILD_KERBEROS, [test x$enable_kerberos != xno])

# Last.fm
AC_DEFINE(GOA_LASTFM_NAME, ["lastfm"], [ProviderType and extension point name])
AC_ARG_ENABLE([lastfm],
              [AS_HELP_STRING([--enable-lastfm], [Enable Last.fm provider])],
              [],
              [enable_lastfm=yes])
AC_ARG_WITH(lastfm-client-id,
            [AS_HELP_STRING([--with-lastfm-client-id], [Last.fm client id])],
            [],
            [])
AC_ARG_WITH(lastfm-client-secret,
            [AS_HELP_STRING([--with-lastfm-client-secret], [Last.fm client secret])],
            [],
            [])
if test "$with_lastfm_client_id" = ""; then
  with_lastfm_client_id="7a2461fe34c9c8124fb28ac750ba12fa"
fi
if test "$with_lastfm_client_secret" = ""; then
  with_lastfm_client_secret="49ec391644459c417f3afe57ca246c5a"
fi
AC_DEFINE_UNQUOTED(GOA_LASTFM_CLIENT_ID, ["$with_lastfm_client_id"], [LastFM client id])
AC_DEFINE_UNQUOTED(GOA_LASTFM_CLIENT_SECRET, ["$with_lastfm_client_secret"], [LastFM client secret])
if test "$enable_lastfm" != "no"; then
  AC_DEFINE(GOA_LASTFM_ENABLED, 1, [Enable LastFM data provider])
fi

# Identity service
#

AM_CONDITIONAL(BUILD_IDENTITY_SERVICE, [test x$enable_fedora != xno || test x$enable_kerberos != xno])

# Optional timerfd support
AC_MSG_CHECKING([for timerfd support])
AC_COMPILE_IFELSE([AC_LANG_PROGRAM([
#include <sys/timerfd.h>
#include <unistd.h>
],[
int
main (void)
{
  struct itimerspec timer_spec = { 0 };
  timerfd_settime (timerfd_create (CLOCK_MONOTONIC, TFD_CLOEXEC),
                   TFD_TIMER_ABSTIME,
                   &timer_spec,
                   NULL);

  return 0;
}
])],
[have_timerfd=yes],
[have_timerfd=no])
AC_MSG_RESULT($have_timerfd)
if test x"$have_timerfd" = x"yes"; then
    AC_DEFINE(HAVE_TIMERFD, 1, [have timerfd support])

    dnl libc headers tend to trail kernel support
    dnl so compensate if necessary
    AC_MSG_CHECKING([for timerfd cancel-on-set support])
    AC_COMPILE_IFELSE([AC_LANG_PROGRAM([
    #include <sys/timerfd.h>
    #include <unistd.h>
    ],[
    int
    main (void)
    {
      struct itimerspec timer_spec = { 0 };
      timerfd_settime (timerfd_create (CLOCK_MONOTONIC, TFD_CLOEXEC),
                       TFD_TIMER_ABSTIME | TFD_TIMER_CANCEL_ON_SET,
                       &timer_spec,
                       NULL);

      return 0;
    }
    ])],
    [have_tfd_timer_cancel_on_set=yes],
    [have_tfd_timer_cancel_on_set=no])
    AC_MSG_RESULT($have_tfd_timer_cancel_on_set)
    if test x"$have_tfd_timer_cancel_on_set" = x"no"; then
       AC_DEFINE(TFD_TIMER_CANCEL_ON_SET, [(1 << 1)], [have timerfd support])
    fi
fi

# Internationalization
#

GETTEXT_PACKAGE=gnome-online-accounts
AC_SUBST([GETTEXT_PACKAGE])
AC_DEFINE_UNQUOTED([GETTEXT_PACKAGE],["$GETTEXT_PACKAGE"],[gettext domain])

# FIXME: Remove AM_GNU_GETTEXT_VERSION once autoreconf supports REQUIRE_VERSION
AX_REQUIRE_DEFINED([AM_GNU_GETTEXT_VERSION])
AM_GNU_GETTEXT_VERSION([0.19.8])

AX_REQUIRE_DEFINED([AM_GNU_GETTEXT_REQUIRE_VERSION])
AM_GNU_GETTEXT_REQUIRE_VERSION([0.19.8])

AX_REQUIRE_DEFINED([AM_GNU_GETTEXT])
AM_GNU_GETTEXT([external])

# goaconfig.h
#

AC_CONFIG_COMMANDS([src/goa/goaconfig.h],
[
	outfile=src/goa/goaconfig.h-tmp
	cat > $outfile <<\_______EOF
/* goaconfig.h
 *
 * This is a generated file.  Please modify 'configure.ac'
 */

#ifndef __GOA_CONFIG_H__
#define __GOA_CONFIG_H__

G_BEGIN_DECLS

_______EOF
	echo "#define GOA_MAJOR_VERSION $GOA_MAJOR_VERSION" >> $outfile
	echo "#define GOA_MINOR_VERSION $GOA_MINOR_VERSION" >> $outfile
	echo "#define GOA_MICRO_VERSION $GOA_MICRO_VERSION" >> $outfile
	cat >> $outfile <<\_______EOF

G_END_DECLS

#endif /* __GOA_CONFIG_H__ */
_______EOF


	if cmp -s $outfile src/goa/goaconfig.h; then
	  AC_MSG_NOTICE([src/goa/goaconfig.h is unchanged])
	  rm -f $outfile
	else
	  mv $outfile src/goa/goaconfig.h
	fi
],[
	GOA_MAJOR_VERSION=$GOA_MAJOR_VERSION
	GOA_MINOR_VERSION=$GOA_MINOR_VERSION
	GOA_MICRO_VERSION=$GOA_MICRO_VERSION
])

# Generate
#

AC_OUTPUT([
Makefile
data/Makefile
data/icons/Makefile
data/icons/scalable/Makefile
data/icons/symbolic/Makefile
src/Makefile
src/goa/Makefile
src/goa/goa-1.0.pc
src/goabackend/Makefile
src/goabackend/goa-backend-1.0.pc
src/goaidentity/Makefile
src/daemon/Makefile
src/examples/Makefile
po/Makefile.in
doc/Makefile
doc/version.xml
])

dnl ==========================================================================
echo "
               gnome-online-accounts $VERSION
             ===============================

	prefix:                         ${prefix}
	libdir:                         ${libdir}
	libexecdir:                     ${libexecdir}
	bindir:                         ${bindir}
	sbindir:                        ${sbindir}
	datadir:                        ${datadir}
	sysconfdir:                     ${sysconfdir}
	localstatedir:                  ${localstatedir}

	compiler:                       ${CC}
	cflags:                         ${CFLAGS}
	cppflags:                       ${CPPFLAGS}

	backend:                        ${enable_backend}
	inspector:		        ${enable_inspector}
	introspection:		        ${found_introspection}
	template file:		        ${with_template_file}

	Fedora Account System provider: ${enable_fedora}
	Flickr provider:                ${enable_flickr} (OAuth 1.0, key:${with_flickr_consumer_key} secret:${with_flickr_consumer_secret})
	Foursquare provider:            ${enable_foursquare} (id:${with_foursquare_client_id})
	Google provider:                ${enable_google} (OAuth 2.0, id:${with_google_client_id} secret:${with_google_client_secret})
	IMAP/SMTP provider:             ${enable_imap_smtp}
	Media Server provider:          ${enable_media_server}
	Microsoft Exchange provider:    ${enable_exchange}
	ownCloud provider:              ${enable_owncloud}
	Kerberos provider:              ${enable_kerberos}
	Facebook provider:              ${enable_facebook} (OAuth 2.0, id:${with_facebook_client_id})
	Windows Live provider:          ${enable_windows_live} (OAuth 2.0, id:${with_windows_live_client_id})
	Last.fm provider:               ${enable_lastfm} (id:${with_lastfm_client_id} secret:${with_lastfm_client_secret})

	Maintainer mode:                ${USE_MAINTAINER_MODE}
	Building api docs:              ${enable_gtk_doc}
"
