#!/bin/bash

# 检查 root 权限
if [ "$EUID" -ne 0 ]; then
    echo "错误：配置开机自启动需要管理员权限。"
    echo "请使用 root 用户或使用 sudo 运行此脚本 (例如: sudo ./install.sh)"
    exit 1
fi

# 设置变量
GITHUB_URL="https://github.com/Toby1GO/Socks5/releases/download/Socks5/work"
WORK_DIR=$(pwd)
EXEC_FILE="$WORK_DIR/work"
SERVICE_NAME="socks5-work"
SERVICE_FILE="/etc/systemd/system/${SERVICE_NAME}.service"

# 1. 依赖检查并执行下载 (默认走 GitHub)
echo "正在从 GitHub 下载程序..."
if command -v wget &> /dev/null; then
    wget -O "$EXEC_FILE" "$GITHUB_URL"
elif command -v curl &> /dev/null; then
    curl -L -o "$EXEC_FILE" "$GITHUB_URL"
else
    echo "错误：系统中未找到 wget 或 curl 命令，脚本退出！"
    exit 1
fi

if [ ! -f "$EXEC_FILE" ]; then
    echo "错误：文件下载失败！"
    exit 1
fi

# 2. 赋予执行权限
chmod +x "$EXEC_FILE"
echo "下载完成并已赋予执行权限。"

# 3. 配置 Systemd 开机自启动
echo "正在配置 Systemd 开机自启动服务..."

cat > "$SERVICE_FILE" <<EOF
[Unit]
Description=Socks5 Work Node Service
After=network.target

[Service]
Type=simple
WorkingDirectory=$WORK_DIR
ExecStart=$EXEC_FILE
Restart=on-failure
RestartSec=5s

[Install]
WantedBy=multi-user.target
EOF

# 4. 重载、启用并启动服务
systemctl daemon-reload
systemctl enable "$SERVICE_NAME"
systemctl start "$SERVICE_NAME"

echo "=============================="
echo "安装成功！程序已设置为开机自启动并在后台运行。"
echo "注意：请勿移动或删除当前目录下的 work 文件 ($EXEC_FILE)。"
echo "=============================="
echo "正在实时输出运行日志 (按 Ctrl+C 退出日志查看)："
echo "------------------------------"

# 5. 实时追踪并显示日志
journalctl -u "$SERVICE_NAME" -f
