#
# Copyright (C) 2017 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=libndpi
PKG_VERSION:=4.0
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/ntop/nDPI/tar.gz/$(PKG_VERSION)?
PKG_HASH:=99e0aba6396fd633c3840f30e4942f6591a08066d037f560b65ba64e7310f4d6
PKG_BUILD_DIR:=$(BUILD_DIR)/nDPI-$(PKG_VERSION)

PKG_MAINTAINER:=Banglang Huang <banglang.huang@foxmail.com>, Toni Uhlig <matzeton@googlemail.com>
PKG_LICENSE:=LGPL-3.0-or-later
PKG_LICENSE_FILES:=COPYING

PKG_FIXUP:=autoreconf
PKG_REMOVE_FILES:=autogen.sh
PKG_BUILD_DEPENDS:=libpcap
PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

ifeq ($(CONFIG_LIBNDPI_GCRYPT),)
CONFIGURE_ARGS += --disable-gcrypt
endif

ifneq ($(CONFIG_LIBNDPI_MAXMINDDB),)
CONFIGURE_ARGS += --with-maxminddb
endif

define Package/libndpi
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE:=Library for deep-packet inspection
  URL:=https://github.com/ntop/nDPI
  DEPENDS:=+LIBNDPI_GCRYPT:libgcrypt +LIBNDPI_MAXMINDDB:libmaxminddb +libpcap +libjson-c
endef

define Package/libndpi/description
  nDPI is an open source LGPLv3 library for deep-packet inspection.
  Based on OpenDPI it includes ntop extensions.
endef

define Package/libndpi/config
config LIBNDPI_GCRYPT
	bool "GCrypt support"
	depends on PACKAGE_libndpi
	default n
	help
	  This option enables QUIC client hello decryption.
	  Disabled by default.

config LIBNDPI_MAXMINDDB
	bool "Maxmind GeoIP support"
	depends on PACKAGE_libndpi
	default n
	help
	  This options enables geographical information processing
	  and serialization based on IP addresses.
	  Disabled by default.
endef

define Build/Prepare
	$(PKG_UNPACK)
	$(Build/Patch)
	mv $(PKG_BUILD_DIR)/configure.seed $(PKG_BUILD_DIR)/configure.ac
	$(SED) "s/@NDPI_MAJOR@/3/g" \
		-e "s/@NDPI_MINOR@/4/g" \
		-e "s/@NDPI_PATCH@/0/g" \
		-e "s/@NDPI_VERSION_SHORT@/3.4.0/g" \
		-e "s/@FUZZY@/dnl> /g" \
		$(PKG_BUILD_DIR)/configure.ac
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include/ndpi
	$(CP) $(PKG_BUILD_DIR)/src/include/*.h \
		$(1)/usr/include/ndpi/

	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_BUILD_DIR)/src/lib/libndpi.so* \
		$(1)/usr/lib/

	$(INSTALL_DIR) $(1)/usr/lib/pkgconfig
	$(CP) $(PKG_BUILD_DIR)/libndpi.pc \
		$(1)/usr/lib/pkgconfig/
endef

define Package/libndpi/install
	$(INSTALL_DIR) $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/src/lib/libndpi.so* \
		$(1)/usr/lib/
	$(INSTALL_DIR) $(1)/usr/bin/
	$(CP) $(PKG_BUILD_DIR)/example/ndpiReader \
		$(1)/usr/bin/
endef

$(eval $(call BuildPackage,libndpi))
