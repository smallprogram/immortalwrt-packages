# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NPM_NAME:=node-ubus
PKG_NAME:=$(PKG_NPM_NAME)
PKG_VERSION:=1.0.0
PKG_RELEASE:=4

PKG_SOURCE:=$(PKG_NPM_NAME)-$(PKG_VERSION).tgz
PKG_SOURCE_URL:=https://mirrors.tencent.com/npm/$(PKG_NPM_NAME)/-/ \
                https://registry.npmjs.org/$(PKG_NPM_NAME)/-/
PKG_HASH:=581c3ce3014091c3d8841b3afcd1e4359ca136bf2f1a67473e79c1dbcbf1f2d1

PKG_MAINTAINER:=Hirokazu MORIKAWA <morikw2@gmail.com>
PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE

PKG_BUILD_DEPENDS:=node/host
PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=no-mips16

include $(INCLUDE_DIR)/package.mk

define Package/node-ubus
  SUBMENU:=Node.js
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=OpenWrt Ubus interface for Node-js
  URL:=https://www.npmjs.com/package/node-ubus
  DEPENDS:=+node +libubus +libjson-c +libblobmsg-json +libubox @NODEJS_24:BROKEN
endef

define Package/node-ubus/description
 NodeJS package to interface with OpenWrt UBus
endef

TAR_OPTIONS+= --strip-components 1
TAR_CMD=$(HOST_TAR) -C $(1) $(TAR_OPTIONS)

NODEJS_CPU:=$(subst powerpc,ppc,$(subst aarch64,arm64,$(subst x86_64,x64,$(subst i386,ia32,$(ARCH)))))
TMPNPM:=$(shell mktemp -u XXXXXXXXXX)
NPM_CACHE_DIR:=$(if $(call qstrip,$(CONFIG_NODEJS_NPM_CACHE_DIR)),$(call qstrip,$(CONFIG_NODEJS_NPM_CACHE_DIR)),$(TMP_DIR))

TARGET_CFLAGS+=$(FPIC)
TARGET_CPPFLAGS+=$(FPIC)

define Build/Compile
	git init $(PKG_BUILD_DIR)
	cd $(PKG_BUILD_DIR); \
	$(MAKE_VARS) \
	$(MAKE_FLAGS) \
	npm_config_nodedir=$(STAGING_DIR)/usr/ \
	$(if $(CONFIG_NODEJS_NPM_KEEP_CACHE), npm_config_cache=$(NPM_CACHE_DIR)/npm-cache-$(PKG_NPM_NAME),npm_config_cache=$(TMP_DIR)/npm-cache-$(TMPNPM)) \
	npm install --prefer-offline --no-audit --global-style --install-strategy=shallow --no-save --omit=dev --no-package-lock --build-from-source --target_arch=$(NODEJS_CPU)
	rm -rf $(TMP_DIR)/npm-cache-$(TMPNPM)
	rm -f $(PKG_BUILD_DIR)/node_modules/.package-lock.json
	find $(PKG_BUILD_DIR)/node_modules -type d -empty -print0 | xargs -0 -r rmdir || true
endef

define Package/node-ubus/install
	$(INSTALL_DIR) $(1)/usr/lib/node/$(PKG_NPM_NAME)
	$(CP) $(PKG_BUILD_DIR)/{package.json,README,LICENSE} \
		$(1)/usr/lib/node/$(PKG_NPM_NAME)/
	$(CP) $(PKG_BUILD_DIR)/{node_modules,*.js} \
		$(1)/usr/lib/node/$(PKG_NPM_NAME)/
	$(INSTALL_DIR) $(1)/usr/lib/node/$(PKG_NPM_NAME)/build/Release
	$(CP) $(PKG_BUILD_DIR)/build/Release/ubus_bindings.node \
		$(1)/usr/lib/node/$(PKG_NPM_NAME)/build/Release/
	$(INSTALL_DIR) $(1)/usr/lib/node_modules
	$(LN) ../node/$(PKG_NPM_NAME) $(1)/usr/lib/node_modules/$(PKG_NPM_NAME)
endef

define Package/node-ubus/postrm
#!/bin/sh
rm /usr/lib/node_modules/node-ubus || true
rm -rf /usr/lib/node/node-ubus || true
endef

$(eval $(call BuildPackage,node-ubus))
