#!/bin/sh

display_usage() {
	echo "USB WiFi AP 設置"
	echo -e "\nUsage: $0 <SSID> <密碼>\n"
	echo -e "例如: $0 OpenWrt 12345678\n"
}

# 如果參數少於 2 個，顯示使用方法
if [ $# -le 1 ]; then
	display_usage
	exit 1
fi

# 如果使用 --help 或 -h 參數，顯示使用方法
if [[ "$1" == "--help" || "$1" == "-h" ]]; then
	display_usage
	exit 0
fi

# 刪除現有的 WiFi 配置並重新生成
rm -f /etc/config/wireless
wifi config

# 使用 uci 批次設置無線配置
uci batch <<EOF
set wireless.radio0.hwmode='11a'
set wireless.radio0.country='TW'                 # 設定為台灣
set wireless.radio0.channel='36'                 # 設定 5GHz 頻道
set wireless.radio0.legacy_rates='0'
set wireless.radio0.mu_beamformer='1'
set wireless.radio0.htmode='VHT80'               # 使用 80MHz 頻寬以提高速度
set wireless.default_radio0.device='radio0'
set wireless.default_radio0.network='lan'
set wireless.default_radio0.mode='ap'
set wireless.default_radio0.ssid=$1              # 使用輸入的參數作為 SSID
set wireless.default_radio0.disassoc_low_ack='0'
set wireless.default_radio0.encryption='psk2+ccmp' # 使用 WPA2-CCMP 加密
set wireless.default_radio0.key=$2               # 使用輸入的參數作為 WiFi 密碼
set wireless.default_radio0.wps_pushbutton='0'
EOF

# 提交更改
uci commit wireless

# 重新加載 WiFi 設置
wifi reload

# 提示是否重啟系統
read -r -p "需要重啟系統以應用更改嗎？[Y/n] " response
case "$response" in
	[yY][eE][sS]|[yY])
		reboot
		;;
	*)
		exit
		;;
esac
