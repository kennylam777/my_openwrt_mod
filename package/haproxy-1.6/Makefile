#
# Copyright (C) 2010-2013 OpenWrt.org
# Copyright (C) 2009-2014 Thomas Heil <heil@terminal-consulting.de>
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=haproxy-1.6
PKG_VERSION:=1.6.6
PKG_RELEASE:=01
PKG_SOURCE:=haproxy-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=http://haproxy.1wt.eu/download/1.6/src/
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(BUILD_VARIANT)/haproxy-$(PKG_VERSION)
PKG_MD5SUM:=b418d83cf4f2da328f6eb8c255b8716b
PKG_MAINTAINER:=Kenny Lam - https://github.com/kennylam777
PKG_LICENSE:=GPL-2.0

include $(INCLUDE_DIR)/package.mk

define Package/$(PKG_NAME)/Default
  SUBMENU:=Web Servers/Proxies
  SECTION:=net
  CATEGORY:=Network
  TITLE:=The Reliable, High Performance TCP/HTTP Load Balancer
  URL:=http://haproxy.1wt.eu/
endef

define Package/$(PKG_NAME)/Default/conffiles
/etc/haproxy.cfg
endef

define Package/$(PKG_NAME)/Default/description
 Open source Reliable, High Performance TCP/HTTP Load Balancer.
endef

define Package/$(PKG_NAME)
  DEPENDS+= +libpcre +libltdl +zlib +libpthread +libopenssl
  TITLE+= (with SSL support)
  VARIANT:=ssl
$(call Package/$(PKG_NAME)/Default)
endef

define Package/$(PKG_NAME)/conffiles
$(call Package/$(PKG_NAME)/Default/conffiles)
endef

define Package/$(PKG_NAME)/description
$(call Package/$(PKG_NAME)/Default/description)
 This package is built with SSL support.
endef

define Package/$(PKG_NAME)-nossl
  TITLE+= (without SSL support)
  VARIANT:=nossl
  DEPENDS+= +libpcre +libltdl +zlib +libpthread
  TITLE+= (with SSL support)
$(call Package/$(PKG_NAME)/Default)
endef

define Package/$(PKG_NAME)-nossl/conffiles
$(call Package/$(PKG_NAME)/Default/conffiles)
endef

define Package/$(PKG_NAME)-nossl/description
$(call Package/$(PKG_NAME)/Default/description)
 This package is built without SSL support.
endef

ifeq ($(CONFIG_avr32),y)
  LINUX_TARGET:=linux26
else
  LINUX_TARGET:=linux2628
endif

ifeq ($(BUILD_VARIANT),ssl)
	USE_OPENSSL=USE_OPENSSL=1
else
	USE_OPENSSL=
endif

define Build/Compile
	$(MAKE) TARGET=$(LINUX_TARGET) -C $(PKG_BUILD_DIR) \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		CC="$(TARGET_CC)" \
		CFLAGS="$(TARGET_CFLAGS) -fno-align-jumps -fno-align-functions -fno-align-labels -fno-align-loops -pipe -fomit-frame-pointer -fhonour-copts" \
		LD="$(TARGET_CC)" \
		LDFLAGS="$(TARGET_LDFLAGS)" \
		PCREDIR="$(STAGING_DIR)/usr" \
		SMALL_OPTS="-DBUFSIZE=16384 -DMAXREWRITE=1030 -DSYSTEM_MAXCONN=165530 " \
		USE_LINUX_TPROXY=1 USE_LINUX_SPLICE=1 USE_REGPARM=1 $(USE_OPENSSL) \
		USE_ZLIB=yes USE_PCRE=1 \
		VERSION="$(PKG_VERSION)-patch$(PKG_RELEASE)"

	$(MAKE) TARGET=$(LINUX_TARGET) -C $(PKG_BUILD_DIR) \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		CC="$(TARGET_CC)" \
		CFLAGS="$(TARGET_CFLAGS) -fno-align-jumps -fno-align-functions -fno-align-labels -fno-align-loops -pipe -fomit-frame-pointer -fhonour-copts" \
		LD="$(TARGET_CC)" \
		LDFLAGS="$(TARGET_LDFLAGS)" \
		PCREDIR="$(STAGING_DIR)/usr" \
		SMALL_OPTS="-DBUFSIZE=16384 -DMAXREWRITE=1030 -DSYSTEM_MAXCONN=165530 " \
		USE_LINUX_TPROXY=1 USE_LINUX_SPLICE=1 USE_REGPARM=1 $(USE_OPENSSL) \
		USE_ZLIB=yes USE_PCRE=1 \
		VERSION="$(PKG_VERSION)-patch$(PKG_RELEASE)" \
		install

	$(MAKE) -C $(PKG_BUILD_DIR)/contrib/halog \
		CC="$(TARGET_CC) $(TARGET_CFLAGS) $(TARGET_LDFLAGS)" \
		OPTIMIZE="" \
		halog
endef

define Package/$(PKG_NAME)/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/haproxy $(1)/usr/sbin/
	$(INSTALL_DIR) $(1)/etc
	$(INSTALL_CONF) ./files/haproxy.cfg $(1)/etc/
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/haproxy.init $(1)/etc/init.d/haproxy
	$(INSTALL_DIR) $(1)/etc/hotplug.d/net
	$(INSTALL_BIN) ./files/haproxy.hotplug $(1)/etc/hotplug.d/net/90-haproxy
endef

Package/$(PKG_NAME)-nossl/install = $(Package/$(PKG_NAME)/install)

define Package/halog-1.6
	MENU:=1
	$(call Package/$(PKG_NAME)/Default)
	TITLE+= halog
	DEPENDS:=$(PKG_NAME)
endef

define Package/halog-1.6/description
	HAProxy Log Analyzer
endef

define Package/halog-1.6/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/contrib/halog-1.6/halog $(1)/usr/bin/
endef

$(eval $(call BuildPackage,$(PKG_NAME)-nossl))
$(eval $(call BuildPackage,$(PKG_NAME)))
$(eval $(call BuildPackage,halog-1.6))
