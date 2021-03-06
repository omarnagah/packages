#
# Copyright (C) 2014 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=sqm-scripts
PKG_SOURCE_VERSION:=ab763cba8b1516b3afa99760e0ca884f8b8d93b8
PKG_VERSION:=1.4.0
PKG_RELEASE:=6

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/tohojo/sqm-scripts
PKG_MIRROR_HASH:=43f59dd4c74c5f1634498c18e370c5185110be1084597df37773cecf306e3a24

PKG_MAINTAINER:=Toke Høiland-Jørgensen <toke@toke.dk>
PKG_LICENSE:=GPL-2.0-only

include $(INCLUDE_DIR)/package.mk

define Package/sqm-scripts
  SECTION:=net
  CATEGORY:=Base system
  DEPENDS:=+tc +kmod-sched-core +kmod-ifb +iptables \
	+iptables-mod-ipopt +iptables-mod-conntrack-extra \
	+!LINUX_4_14:kmod-sched-cake +LINUX_4_14:kmod-sched-cake-oot
  TITLE:=SQM Scripts (QoS)
  PKGARCH:=all
endef

define Package/sqm-scripts/description
 A set of scripts that does simple SQM configuration.
endef

define Package/sqm-scripts/conffiles
/etc/config/sqm
/etc/sqm/sqm.conf
endef

define Package/sqm-scripts/install
	make -C $(PKG_BUILD_DIR) DESTDIR=$(1) PLATFORM=openwrt install
endef

define Package/luci-app-sqm
  SECTION:=luci
  CATEGORY:=LuCI
  TITLE:=SQM Scripts - LuCI interface
  PKGARCH:=all
  DEPENDS:=+luci-compat +sqm-scripts
  SUBMENU:=3. Applications
endef

define Package/luci-app-sqm/description
	Luci interface for the SQM scripts queue management configuration package.
endef

define Package/luci-app-sqm/install
	$(MAKE) -C $(PKG_BUILD_DIR) DESTDIR=$(1) PLATFORM=openwrt install-luci
	$(INSTALL_DIR) $(1)/etc/uci-defaults
	$(INSTALL_BIN) ./files/luci-app-sqm.defaults $(1)/etc/uci-defaults
endef

define Package/luci-app-sqm/postrm
#!/bin/sh
which uci > /dev/null || exit 0
uci -q get ucitrack.@sqm[0] > /dev/null && {
  uci delete ucitrack.@sqm[0]
  uci del_list ucitrack.@firewall[0].affects=sqm
  uci commit
} || exit 0
endef

$(eval $(call BuildPackage,sqm-scripts))
$(eval $(call BuildPackage,luci-app-sqm))
