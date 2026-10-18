#
# Copyright (C) 2025 guangyin53222
#
# This is free software, licensed under the Apache-2.0 License.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=athena-led
PKG_VERSION:=2.5.0
PKG_RELEASE:=1

PKG_MAINTAINER:=guangyin53222 <https://github.com/guangyin53222>
PKG_LICENSE:=Apache-2.0
PKG_LICENSE_FILES:=LICENSE

# 纯本地打包：无 PKG_SOURCE / PKG_SOURCE_URL / PKG_HASH
# 彻底解决 CI 哈希不匹配与网络下载问题

include $(INCLUDE_DIR)/package.mk

define Package/$(PKG_NAME)
  SECTION:=utils
  CATEGORY:=Utilities
  # 限制 AX6600 (ipq807x) aarch64 架构，防其他平台误选报错
  DEPENDS:=@TARGET_qualcommax_ipq807x +libc
  TITLE:=Athena LED controller (backend)
endef

define Build/Prepare
	mkdir -p $(PKG_BUILD_DIR)
	$(CP) ./files/* $(PKG_BUILD_DIR)/
endef

define Build/Compile
	# 使用预编译二进制，跳过编译步骤
	true
endef

define Package/$(PKG_NAME)/install
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/athena_led.init $(1)/etc/init.d/athena_led

	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/athena_led.config $(1)/etc/config/athena_led

	# 安装核心二进制（对应图中文件名 athena-led）
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) ./files/athena-led $(1)/usr/bin/athena_led

	# 安装按钮脚本
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) ./files/find_button.sh $(1)/usr/sbin/find_button.sh

	# 安装动画资源目录
	if [ -d ./files/anim ]; then \
		$(INSTALL_DIR) $(1)/usr/share/athena-led/anim; \
		$(CP) ./files/anim/* $(1)/usr/share/athena-led/anim/; \
	fi
endef

$(eval $(call BuildPackage,$(PKG_NAME)))
