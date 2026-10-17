# 🌟 [包拆分 + JS化] luci-app-athena-led — 纯 Web 界面包 (v2.5.0)
# 核心程序/init/config/动画 已拆到 athena-led 包 (见 ../athena-led/Makefile)
#
# v2.3.0 起界面为 LuCI JS 架构 (htdocs/)，不再依赖 luci-compat，
# 彻底解决 QWRT 等新版固件上 "装完 Lua 插件整个管理页崩溃" 的问题。

include $(TOPDIR)/rules.mk

PKG_NAME:=luci-app-athena-led
PKG_VERSION:=2.5.0
PKG_RELEASE:=1

PKG_MAINTAINER:=guangyin53222 <https://github.com/guangyin53222>
PKG_LICENSE:=Apache-2.0
PKG_LICENSE_FILES:=LICENSE

include $(INCLUDE_DIR)/package.mk

define Package/$(PKG_NAME)
	SECTION:=luci
	CATEGORY:=LuCI
	SUBMENU:=3. Applications
	TITLE:=LuCI Support for Athena LED (JS UI)
	URL:=https://github.com/guangyin53222/JDC-AX6600-Athena-LED-Controller
	DEPENDS:=+athena-led +luci-base
	BUILD_DEPENDS:=luci-base/host
	PKGARCH:=all
endef

define Package/$(PKG_NAME)/description
Web UI for the JDCloud AX6600 (Athena) LED screen controller.
v2.3.0+: modern LuCI JS architecture, no luci-compat needed.
Split from core athena-led package.
endef

define Build/Compile
	# 如果存在中文语言包目录，则编译为 .lmo 格式
	[ -d ./po/zh_Hans ] && po2lmo ./po/zh_Hans/athena_led.po $(PKG_BUILD_DIR)/zh_Hans.lmo || true
endef

define Package/$(PKG_NAME)/install
	# 1. JS 视图
	$(INSTALL_DIR) $(1)/www/luci-static/resources/view/athena_led
	$(INSTALL_DATA) ./htdocs/luci-static/resources/view/athena_led/settings.js \
		$(1)/www/luci-static/resources/view/athena_led/

	# 2. 菜单注册 (JSON 声明式，取代 Lua controller)
	$(INSTALL_DIR) $(1)/usr/share/luci/menu.d
	$(INSTALL_DATA) ./root/usr/share/luci/menu.d/luci-app-athena-led.json \
		$(1)/usr/share/luci/menu.d/

	# 3. rpcd 权限声明 (uci 读写 / 状态查询 / 服务控制)
	$(INSTALL_DIR) $(1)/usr/share/rpcd/acl.d
	$(INSTALL_DATA) ./root/usr/share/rpcd/acl.d/luci-app-athena-led.json \
		$(1)/usr/share/rpcd/acl.d/

	# 4. i18n 语言包 (双后缀通杀版)
	$(INSTALL_DIR) $(1)/usr/lib/lua/luci/i18n
	[ -f $(PKG_BUILD_DIR)/zh_Hans.lmo ] && { \
		$(INSTALL_DATA) $(PKG_BUILD_DIR)/zh_Hans.lmo $(1)/usr/lib/lua/luci/i18n/athena_led.zh-cn.lmo; \
		$(INSTALL_DATA) $(PKG_BUILD_DIR)/zh_Hans.lmo $(1)/usr/lib/lua/luci/i18n/athena_led.zh_Hans.lmo; \
	} || true
endef

define Package/$(PKG_NAME)/postinst
#!/bin/sh
if [ -z "$${IPKG_INSTROOT}" ]; then
	rm -rf /tmp/luci-indexcache /tmp/luci-modulecache/* 2>/dev/null
	# acl 变更需要 rpcd 重载才能生效
	/etc/init.d/rpcd reload >/dev/null 2>&1
fi
exit 0
endef

$(eval $(call BuildPackage,$(PKG_NAME)))
