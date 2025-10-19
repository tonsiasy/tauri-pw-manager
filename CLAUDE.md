# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A desktop password manager built with Tauri, featuring a Rust backend and React/TypeScript frontend. The application uses strong cryptography to protect user credentials with a master password-based encryption scheme.

## Development Commands

```bash
# Initial setup
pnpm run setup         # Installs tauri-cli and frontend dependencies

# Development
cargo tauri dev        # Run app in development mode (starts both Vite dev server and Tauri)
pnpm run tauri:dev     # Same as above (npm script alias)
pnpm run dev           # Run only the Vite dev server (port 3000)

# Building
cargo tauri build      # Build release version with platform-specific installers
pnpm run tauri:build   # Same as above (npm script alias)
pnpm run build         # Build only the frontend (outputs to ui/build/)

# Build specific formats (see BUILD.md for details)
cargo tauri build --bundles dmg      # macOS DMG only
cargo tauri build --bundles msi      # Windows MSI only
cargo tauri build --bundles deb      # Linux DEB only
cargo tauri build --bundles appimage # Linux AppImage only

# Testing
cargo test             # Run Rust unit tests
```

## Build Output

After running `cargo tauri build`, installers are located in:
```
src-tauri/target/release/bundle/
├── dmg/           # macOS .dmg installer
├── macos/         # macOS .app bundle
├── msi/           # Windows .msi installer
├── nsis/          # Windows NSIS .exe installer
├── deb/           # Linux .deb package
└── appimage/      # Linux .AppImage
```

See BUILD.md for detailed build instructions and platform-specific requirements.

## Cryptography Architecture

The application implements a layered encryption scheme:

1. **Master Key Derivation**: User password + username → PBKDF2-HMAC-SHA256 (100k iterations) → 32-byte master key
2. **Data Encryption Key (DEK)**: Random 32-byte key generated per user account
3. **Key Encryption**: DEK encrypted with master key using AES-256-CTR
4. **Database Encryption**: Credentials database encrypted with DEK using AES-256-GCM with authentication

**File Structure** (`.pwdb` files stored in `~/.config/tauri-pw-manager/` or `%APPDATA%\tauri-pw-manager\`):
- Bytes 0-15: Nonce for key encryption (16 bytes)
- Bytes 16-47: Encrypted DEK (32 bytes)
- Bytes 48+: Encrypted credentials database (IV: 12 bytes, Tag: 16 bytes, Data: variable)

See `src/cryptography.rs` for implementation details and the `EncryptedBlob` type.

## Code Architecture

### Backend (Rust)

**Core modules:**
- `src/main.rs` - Tauri commands, session state management, and app entry point
- `src/cryptography.rs` - All cryptographic operations (PBKDF2, AES-GCM, AES-CTR, password generation)
- `src/database.rs` - `CredentialsDatabase` type and operations
- `src/error.rs` - Error types serialized to JSON for frontend consumption
- `src/logs.rs` - Structured logging with custom macros (`debug!`, `info!`, `error!`)

**Session Management:**
The `UserSession` struct (in `main.rs`) maintains the logged-in user's state:
- Encrypted database file path
- Decrypted DEK (held in memory only while logged in)
- Loaded credentials database
- Wrapped in `Mutex<Option<UserSession>>` for thread-safe access

**Tauri Commands:**
All frontend-backend communication uses Tauri commands (marked with `#[tauri::command]`). Key commands include:
- `create_account`, `login`, `logout`
- `add_credentials`, `remove_credentials`, `fetch_credentials`, `get_credentials_info`
- `generate_password`, `copy_to_clipboard`
- `window_close`, `window_minimize`, `window_toggle_fullscreen`

### Frontend (React/TypeScript)

**Structure:**
- `ui/main.tsx` - App root, theme, page navigation, Material-UI setup
- `ui/backend.tsx` - Type-safe wrappers for all Tauri invoke calls
- `ui/LoginPage.tsx`, `ui/SignUpPage.tsx`, `ui/StartPage.tsx`, `ui/AddPage.tsx` - Page components
- `ui/utils.tsx` - Shared utilities and contexts

**Navigation:**
Page flow is managed in `main.tsx` with a state-based router:
- login → signup (account creation)
- login → start (after successful login, shows credentials list)
- start → add (add new credential entry)

**Backend Integration:**
All backend calls in `backend.tsx` return `{ ok: true, value: T } | { ok: false, error: string }` for consistent error handling.

## Important Constraints

- `#![forbid(unsafe_code)]` - No unsafe Rust code allowed
- Custom window decorations (`decorations: false` in Tauri.toml) - titlebar is custom React component
- No network calls - fully offline password manager
- Logs are automatically cleaned after 3 days (see `logs.rs:remove_old`)
- Password generation uses cryptographically secure randomness with modulo bias mitigation

## Configuration Files

- `Tauri.toml` - Tauri configuration (uses TOML format, not deprecated JSON)
- `Cargo.toml` - Rust dependencies (note: requires OpenSSL)
- `package.json` - Frontend dependencies (uses pnpm, Volta for Node version management)
- `vite.config.ts` - Frontend build config (root: `./ui`, port: 3000)

## Testing

Tests are written using Rust's built-in test framework. Existing tests cover:
- Encryption/decryption roundtrips (`cryptography.rs`)
- Binary serialization (`EncryptedBlob::from_bytes`)
- Password generation correctness
- Log file rotation logic

When adding cryptographic features, always add corresponding tests.
