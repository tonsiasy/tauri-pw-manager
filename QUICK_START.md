# 快速开始 Quick Start

## 一、安装依赖

```bash
# 1. 安装 Rust (如果未安装)
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# 2. 安装 Node.js 和 pnpm (如果未安装)
# 使用 Volta (推荐)
curl https://get.volta.sh | bash
volta install node@18.5.0
npm install -g pnpm

# 3. 安装项目依赖
pnpm run setup
```

## 二、开发运行

```bash
# 启动开发模式 (热重载)
cargo tauri dev
```

## 三、构建安装包

### macOS
```bash
# 构建 DMG 和 APP
cargo tauri build

# 输出位置：
# src-tauri/target/release/bundle/dmg/Password Manager_0.1.0_x64.dmg
# src-tauri/target/release/bundle/macos/Password Manager.app
```

### Windows
```bash
# 构建 MSI 和 EXE 安装程序
cargo tauri build

# 输出位置：
# src-tauri/target/release/bundle/msi/Password Manager_0.1.0_x64_en-US.msi
# src-tauri/target/release/bundle/nsis/Password Manager_0.1.0_x64-setup.exe
```

### Linux
```bash
# 构建 DEB 和 AppImage
cargo tauri build

# 输出位置：
# src-tauri/target/release/bundle/deb/password-manager_0.1.0_amd64.deb
# src-tauri/target/release/bundle/appimage/password-manager_0.1.0_amd64.AppImage
```

## 四、安装应用

### macOS
1. 双击 `.dmg` 文件
2. 将 `Password Manager.app` 拖到 Applications 文件夹

或直接运行：
```bash
open "src-tauri/target/release/bundle/macos/Password Manager.app"
```

### Windows
双击 `.msi` 或 `.exe` 文件，按照安装向导操作

### Linux

**DEB (Debian/Ubuntu):**
```bash
sudo dpkg -i src-tauri/target/release/bundle/deb/password-manager_0.1.0_amd64.deb
```

**AppImage:**
```bash
chmod +x src-tauri/target/release/bundle/appimage/password-manager_0.1.0_amd64.AppImage
./src-tauri/target/release/bundle/appimage/password-manager_0.1.0_amd64.AppImage
```

## 五、常见问题

### OpenSSL 错误 (macOS)
```bash
brew install openssl
export OPENSSL_DIR=$(brew --prefix openssl)
```

### WebKit 错误 (Linux)
```bash
# Ubuntu/Debian
sudo apt install libwebkit2gtk-4.0-dev

# Fedora
sudo dnf install webkit2gtk3-devel
```

### 构建失败
```bash
# 清理并重新构建
cargo clean
rm -rf ui/build node_modules
pnpm install
pnpm run build
cargo tauri build
```

## 更多信息

- 详细构建说明: [BUILD.md](BUILD.md)
- 开发文档: [CLAUDE.md](CLAUDE.md)
- 项目说明: [README.md](README.md)
