#
# Copyright (C) 2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=whsnbg
PKG_VERSION:=1.1
PKG_RELEASE:=1
PKG_LICENSE:=GPL-2.0
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=Vladimir Alemasov <homewsn.com@gmail.com>

PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_SOURCE_SUBDIR)

# Remote source
PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/homewsn/whsnbg.git
# Always the latest version
PKG_SOURCE_VERSION:=HEAD
# Version 1.1
#PKG_SOURCE_VERSION:=63236f501b3ba1b09a152fa252e9a783270208da
PKG_SOURCE:=$(PKG_SOURCE_SUBDIR).tar.gz


PKG_BUILD_PARALLEL:=1

PKG_INCLPATH:= -I. -I$(STAGING_DIR)/usr/include/mysql
PKG_LIBPATH:= -L. -L$(STAGING_DIR)/usr/lib/mysql

# Libraries dependencies
PKG_LIBS:=

# Package dependencies
PKG_DPNDS:= +libc +libpthread
PKG_DPNDS+= +WHSNBG_AXTLS_LIBRARY:libaxtls +WHSNBG_OPENSSL_LIBRARY:libopenssl +(WHSNBG_SENSOR_DATA_MYSQL||WHSNBG_MQTT_DATA_MYSQL):libmysqlclient

# Defines
PKG_DEFS:= -DOPENWRT -DNDEBUG

ifneq ($(CONFIG_WHSNBG_DAEMON),)
  PKG_DEFS+= -DLINUX_DAEMON_VERSION
endif
ifeq ($(CONFIG_WHSNBG_DPRINTF),)
  PKG_DEFS+= -DNDPRINTF
endif
ifneq ($(CONFIG_WHSNBG_RULES_ENGINE),)
  PKG_DEFS+= -DRULES_ENGINE
endif
ifneq ($(CONFIG_WHSNBG_AXTLS_LIBRARY),)
  PKG_DEFS+= -DAXTLS_LIBRARY
  PKG_LIBS+= -laxtls
endif
ifneq ($(CONFIG_WHSNBG_OPENSSL_LIBRARY),)
  PKG_DEFS+= -DOPENSSL_LIBRARY
  PKG_LIBS+= -lssl -lcrypto
  ifneq ($(CONFIG_WHSNBG_STATIC),)
    PKG_LIBS+= -lz
  endif
endif
ifneq ($(CONFIG_WHSNBG_SENSOR_DATA),)
  PKG_DEFS+= -DSENSOR_DATA
endif
ifneq ($(CONFIG_WHSNBG_SENSOR_DATA_MYSQL),)
  PKG_DEFS+= -DSENSOR_DATA_MYSQL
endif
ifneq ($(CONFIG_WHSNBG_MQTT_DATA_MYSQL),)
  PKG_DEFS+= -DMQTT_DATA_MYSQL
endif
ifneq ($(CONFIG_WHSNBG_SENSOR_DATA_MYSQL),$(filter $(CONFIG_WHSNBG_SENSOR_DATA_MYSQL),))
  PKG_LIBS+= -lmysqlclient
  ifneq ($(CONFIG_WHSNBG_STATIC),)
    PKG_LIBS+= -lz
  endif
endif
PKG_LIBS += -lpthread -lgcc_eh -lm
ifneq ($(CONFIG_WHSNBG_STATIC),)
  PKG_DPNDS:=
  PKG_LIBS+= -ldl -static
endif

include $(INCLUDE_DIR)/package.mk

define Package/whsnbg/config
  source "$(SOURCE)/Config.in"
endef

define Package/whsnbg/default
  SECTION:=base
  CATEGORY:=Network
  URL:=http://www.homewsn.com/
endef

define Package/whsnbg
  $(Package/whsnbg/default)
  TITLE:=MQTT broker and rules engine, MQTT-SN gateway, MySQL WSN client
  DEPENDS:=$(PKG_DPNDS)
  MENU:=1
endef

define Package/whsnbg-cc2531-usb
  $(Package/whsnbg/default)
  DEFAULT:=n
  TITLE:=Whsnbg script files for cc2531 usb dongle
#  DEPENDS:=@USB_SUPPORT whsnbg +kmod-slip +kmod-usb-acm +net-tools-slattach
  DEPENDS:=whsnbg +kmod-slip +kmod-usb-acm +net-tools-slattach
endef

define Package/whsnbg/conffiles
/etc/whsnbg.conf
endef

define Package/whsnbg-cc2531-usb/conffiles
/etc/init.d/cc2531slip
/etc/hotplug.d/usb/20-cc2531
endef

define Package/whsnbg/description
 This package provides a daemon which implements MQTT broker and simple
 MQTT rules engine, MQTT-SN gateway, and MySQL client for sensor networks.
endef

define Package/whsnbg-cc2531-usb/description
 dsdfgsdfgdf
endef

# Local source in /src directory
#define Build/Prepare
#	mkdir -p $(PKG_BUILD_DIR)/src
#	$(CP) ./src/* $(PKG_BUILD_DIR)/src/
#endef

define Build/Compile
	CC="$(TARGET_CC)" \
	CFLAGS="$(TARGET_CFLAGS)" \
	LDFLAGS="$(TARGET_LDFLAGS)" \
	TARGET_INCLPATH="$(PKG_INCLPATH)" \
	TARGET_LIBPATH="$(PKG_LIBPATH)" \
	TARGET_LIBS="$(PKG_LIBS)" \
	TARGET_DEFS="$(PKG_DEFS)" \
	$(MAKE) openwrt -C $(PKG_BUILD_DIR)/src
endef

define Package/whsnbg/install
	$(INSTALL_DIR) $(1)/bin $(1)/etc
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/src/whsnbg $(1)/bin/
	$(INSTALL_DATA) ./files/whsnbg.conf $(1)/etc/
endef

define Package/whsnbg-cc2531-usb/install
	$(INSTALL_DIR) $(1)/etc/init.d $(1)/etc/hotplug.d/usb
	$(INSTALL_BIN) ./files/cc2531slip $(1)/etc/init.d/
	$(INSTALL_DATA) ./files/20-cc2531 $(1)/etc/hotplug.d/usb/
endef

define Package/whsnbg-cc2531-usb/postinst
#!/bin/sh
# check if we are on real system
if [ -z "$${IPKG_INSTROOT}" ]; then
        echo "Enabling rc.d symlink for cc2531slip."
        /etc/init.d/cc2531slip enable
fi
exit 0
endef

define Package/whsnbg-cc2531-usb/prerm
#!/bin/sh
# check if we are on real system
if [ -z "$${IPKG_INSTROOT}" ]; then
        echo "Removing rc.d symlink for cc2531slip."
        /etc/init.d/cc2531slip disable
fi
exit 0
endef

$(eval $(call BuildPackage,whsnbg))
$(eval $(call BuildPackage,whsnbg-cc2531-usb))
