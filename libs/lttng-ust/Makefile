#
# Copyright (C) 2013-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=lttng-ust
PKG_VERSION:=2.13.9
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.bz2
PKG_SOURCE_URL:=https://lttng.org/files/$(PKG_NAME)/
PKG_HASH:=2ad6d69a54a1d924c18a4aa7a233db104e3cc332bcdd240e196bf7adbed3f712

PKG_MAINTAINER:=
PKG_LICENSE:=LGPL-2.1 GPL-2.0
PKG_LICENSE_FILES:=COPYING
PKG_CPE_ID:=cpe:/a:lttng:ust

PKG_FIXUP:=autoreconf
PKG_BUILD_FLAGS:=no-mips16
PKG_BUILD_PARALLEL:=1
PKG_INSTALL:=1

include $(INCLUDE_DIR)/package.mk

define Package/lttng-ust
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE:=Linux Trace Toolkit: next generation (library)
  URL:=https://lttng.org/
  DEPENDS:= +liburcu +libuuid +librt
endef

CONFIGURE_ARGS += \
	--disable-numa \
	--disable-python-agent \
	--disable-examples \
	--disable-man-pages \
	--with-pic

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include
	$(CP) $(PKG_INSTALL_DIR)/usr/include/lttng $(1)/usr/include/
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/liblttng-ust*.so* $(1)/usr/lib/
	$(INSTALL_DIR) $(1)/usr/lib/pkgconfig
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/pkgconfig/lttng-ust* $(1)/usr/lib/pkgconfig/
endef

define Package/lttng-ust/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/liblttng*.so.* $(1)/usr/lib/
endef

$(eval $(call BuildPackage,lttng-ust))
