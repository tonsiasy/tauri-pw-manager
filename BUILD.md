# 构建指南 / Build Guide

本文档说明如何将项目打包成可安装的应用程序。

## 前置要求

### macOS
- Xcode Command Line Tools: `xcode-select --install`
- Rust: https://rustup.rs/
- Node.js 18.5.0 (推荐使用 Volta)
- pnpm: `npm install -g pnpm`

### Windows
- Microsoft C++ Build Tools
- Rust: https://rustup.rs/
- Node.js 18.5.0
- pnpm: `npm install -g pnpm`

### Linux
```bash
# Debian/Ubuntu
sudo apt update
sudo apt install libwebkit2gtk-4.0-dev \
  build-essential \
  curl \
  wget \
  libssl-dev \
  libgtk-3-dev \
  libayatana-appindicator3-dev \
  librsvg2-dev

# Arch
sudo pacman -S webkit2gtk base-devel curl wget openssl gtk3 libappindicator-gtk3 librsvg

# Fedora
sudo dnf install webkit2gtk3-devel.x86_64 openssl-devel curl wget libappindicator-gtk3 librsvg2-devel
sudo dnf group install "C Development Tools and Libraries"
```

## 初始设置

```bash
# 克隆仓库后首次运行
pnpm run setup
```

## 构建命令

### 开发模式
```bash
# 运行开发服务器
cargo tauri dev
# 或使用 npm scripts
pnpm run tauri:dev
```

### 生产构建
```bash
# 构建发布版本
cargo tauri build
# 或使用 npm scripts
pnpm run tauri:build

# 构建调试版本（体积更大但包含调试信息）
pnpm run tauri:build:debug
```

### 指定平台打包格式

macOS:
```bash
# 只构建 DMG
cargo tauri build --bundles dmg

# 只构建 APP
cargo tauri build --bundles app
```

Windows:
```bash
# 只构建 MSI
cargo tauri build --bundles msi

# 只构建 NSIS 安装程序
cargo tauri build --bundles nsis
```

Linux:
```bash
# 只构建 DEB 包
cargo tauri build --bundles deb

# 只构建 AppImage
cargo tauri build --bundles appimage
```

## 输出位置

构建完成后，安装包位于：
```
src-tauri/target/release/bundle/
├── dmg/           # macOS DMG (如果构建)
├── macos/         # macOS APP (如果构建)
├── msi/           # Windows MSI (如果构建)
├── nsis/          # Windows NSIS (如果构建)
├── deb/           # Linux DEB (如果构建)
└── appimage/      # Linux AppImage (如果构建)
```

## 跨平台构建

Tauri 不直接支持跨平台构建，需要在目标平台上构建：
- macOS 应用需要在 macOS 上构建
- Windows 应用需要在 Windows 上构建
- Linux 应用需要在 Linux 上构建

可以使用 CI/CD（如 GitHub Actions）来自动化多平台构建。

## 代码签名

### macOS
需要 Apple Developer 账号和证书：
```bash
# 设置环境变量
export APPLE_CERTIFICATE="你的证书名称"
export APPLE_CERTIFICATE_PASSWORD="证书密码"
export APPLE_SIGNING_IDENTITY="Developer ID Application: Your Name"

cargo tauri build
```

### Windows
在 `Tauri.toml` 中配置：
```toml
[tauri.bundle.windows]
certificateThumbprint = "YOUR_CERTIFICATE_THUMBPRINT"
digestAlgorithm = "sha256"
timestampUrl = "http://timestamp.digicert.com"
```

## 常见问题

### OpenSSL 错误
macOS:
```bash
brew install openssl
export OPENSSL_DIR=$(brew --prefix openssl)
```

### Webkit 错误 (Linux)
确保已安装 webkit2gtk-4.0-dev

### 构建失败
```bash
# 清理构建缓存
cargo clean
rm -rf ui/build
pnpm run build
cargo tauri build
```

## 版本号更新

同时更新以下文件中的版本号：
- `Tauri.toml` - [package] version
- `Cargo.toml` - [package] version
- `package.json` - version

## 应用信息配置

在 `Tauri.toml` 中配置：
- `productName`: 应用显示名称
- `identifier`: 应用唯一标识符（如 com.company.app）
- `category`: 应用类别
- `shortDescription`: 简短描述
- `longDescription`: 详细描述

## 更多信息

- Tauri 文档: https://tauri.app/
- 打包指南: https://tauri.app/v1/guides/building/
- 代码签名: https://tauri.app/v1/guides/distribution/sign-macos
