#
# Copyright (C) 2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=whsniff
PKG_VERSION:=1.0
PKG_RELEASE:=1
PKG_LICENSE:=GPL-2.0
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=Vladimir Alemasov <homewsn.com@gmail.com>

PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_SOURCE_SUBDIR)

# Remote source
PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/homewsn/whsniff.git
# Always the latest version
PKG_SOURCE_VERSION:=HEAD
# Version 1.0
#PKG_SOURCE_VERSION:=0d49ffcd13db3c48ff432464f2cb72553555ca9a
PKG_SOURCE:=$(PKG_SOURCE_SUBDIR).tar.gz


PKG_BUILD_PARALLEL:=1

PKG_INCLPATH:= -I.
PKG_LIBPATH:= -L.

# Libraries dependencies
PKG_LIBS:= -lusb-1.0 -lrt

# Defines
PKG_DEFS:= -DOPENWRT -DNDEBUG


include $(INCLUDE_DIR)/package.mk

define Package/$(PKG_NAME)
  SECTION:=base
  CATEGORY:=Network
  URL:=http://www.homewsn.com/
  TITLE:=Packet converter for sniffing IEEE 802.15.4 WSN
  DEPENDS:=+libc +libusb-1.0 +librt
endef

define Package/$(PKG_NAME)/description
  Whsniff is a command line utility that interfaces TI CC2531 USB dongle
  with Wireshark for capturing and displaying IEEE 802.15.4 traffic.
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
    $(MAKE) openwrt -C $(PKG_BUILD_DIR)/src
endef

define Package/$(PKG_NAME)/install
	$(INSTALL_DIR) $(1)/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/src/whsniff $(1)/bin/
endef

$(eval $(call BuildPackage,$(PKG_NAME)))
