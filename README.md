# tauri-pw-manager (working title)
A desktop password manager using Tauri, with a backend in Rust and a frontend in Typescript and React.

![login page](./assets/login-page.png)

## Cryptography architecture
![cryptography architecture](./assets/crypto-architecture.png)

## Usage

### Prerequisites
- [Rust toolchain](https://rustup.rs/)
- Node.js 18.5.0 (or use [Volta](https://volta.sh/))
- [pnpm](https://pnpm.io/)

### Development

```bash
pnpm run setup       # Install dependencies (first time only)
cargo tauri dev      # Run the app in development mode
# or
pnpm run tauri:dev
```

### Building for Production

```bash
cargo tauri build    # Build release version with installers
# or
pnpm run tauri:build
```

The built installers will be in `src-tauri/target/release/bundle/`:
- **macOS**: `.dmg` and `.app` files
- **Windows**: `.msi` and `.exe` installers
- **Linux**: `.deb` and `.AppImage` packages

For detailed build instructions, platform-specific requirements, and troubleshooting, see [BUILD.md](BUILD.md).
