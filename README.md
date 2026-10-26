# VaultForge 🔐

**A private, local-first password manager and secure password generator for Windows.**

VaultForge is a PHP 8.3+ application that runs on your own PC at `http://127.0.0.1:8080`. It combines a password generator, encrypted vault, passphrase generator, auto-locking, and encrypted backup/restore—without a cloud account, external framework, Node.js, MySQL, CDN, or telemetry.

> **Important:** VaultForge is designed for one user on their local Windows machine. Do not expose the built-in PHP server to your LAN or the public internet. Your master password cannot be recovered if you forget it.

## ✨ Features

- **Secure password generator** — generate passwords from 8–128 characters, customize character sets, and exclude ambiguous characters.
- **Passphrase generator** — create memorable multi-word passphrases with configurable separators, capitalization, and numbers.
- **Encrypted password vault** — save and manage website names, URLs, usernames, passwords, notes, categories, and favourites.
- **Search and organization** — search entries, filter by category, sort, and view favourites.
- **Password insights** — dashboard summaries for weak and reused passwords, categories, and recently updated entries.
- **Auto-lock** — configurable inactivity timeout, manual lock, and a 12-hour absolute session limit.
- **Encrypted backups** — export and restore backups protected by a separate backup password, with merge and replace options.
- **Master password change** — rotates the vault data key and re-encrypts saved entries.
- **Local-first by design** — no analytics, telemetry, remote fonts, CDNs, or external network requests.
- **Security-focused implementation** — CSRF protection, request validation, session hardening, security headers, login throttling, and prepared SQL statements.

## 🛠️ Technology

- PHP 8.3+
- SQLite via PDO
- libsodium (`sodium`) for key derivation and authenticated encryption
- HTML, CSS, and vanilla JavaScript
- Windows PowerShell startup and test scripts

No Composer, Node.js, MySQL, or third-party PHP framework is required.

## 💻 Requirements

- Windows 10 or Windows 11
- PHP **8.3 or newer**
- PHP extensions: `sodium` and `pdo_sqlite`
- `mbstring` is optional
- Approximately **300 MB free RAM** for the configured Argon2id key derivation
- A modern browser such as Chrome, Edge, or Firefox

The included server configuration is in `config/php-server.ini`. The launch scripts use it to enable the required extensions independently of your global `php.ini`.

## 🚀 Quick start

1. Download or clone this repository to your Windows PC.
2. Confirm PHP 8.3+ is installed and available on your `PATH`.
3. Open the project folder.
4. Double-click **`start.bat`**, or run the commands below in PowerShell:

```powershell
.\start.ps1
```

5. Open `http://127.0.0.1:8080` if your browser does not open automatically.
6. On first launch, create a strong master password and keep it safe.

To stop VaultForge, double-click `stop.bat` or run:

```powershell
.\stop.ps1
```

### Run in the foreground

```powershell
.\start.ps1 -Foreground
```

Press `Ctrl+C` to stop the foreground server.

### Use a different port

```powershell
.\start.ps1 -Port 8081
```

Then open `http://127.0.0.1:8081`.

### Check your environment

```powershell
php bin/check.php
```

If PHP is not on `PATH`, set the `VAULTFORGE_PHP` environment variable to the full path of `php.exe`.

## 🔒 Security overview

VaultForge is built to protect stored data at rest and reduce common web-application risks.

- **Key derivation:** Argon2id derives a key from the master password using a random salt.
- **Vault encryption:** a random data-encryption key is wrapped by the derived key and encrypts sensitive entry fields using XChaCha20-Poly1305 authenticated encryption.
- **No stored master password:** VaultForge does not store the master password or a conventional password verifier.
- **Context-bound encryption:** encrypted values are associated with an entry and field, helping detect ciphertext substitution.
- **Local-only server:** the app binds to `127.0.0.1` and rejects non-loopback clients and unapproved Host headers.
- **Request protection:** CSRF tokens, Origin and Fetch Metadata checks, server-side validation, and security headers.
- **Session protection:** strict cookie settings, session ID regeneration, throttled unlock attempts, manual lock, and automatic locking.
- **Encrypted backups:** backup files use a separate password and integrity verification; plaintext export is not provided.

### Understand the limitations

- **There is no master-password recovery or backdoor.** If you forget the master password and have no usable backup, your vault may be permanently inaccessible.
- SQLite itself is **not encrypted as a whole**. Sensitive vault fields are encrypted by the application; some operational metadata remains visible.
- Malware, a compromised Windows account, or a malicious browser extension may access data while the vault is unlocked.
- Clipboard clearing is best-effort. Clipboard managers and Windows clipboard history may retain copied values.
- A stolen database can still be targeted by offline password guesses. Use a strong, unique master password.
- The PHP built-in server is intended for local development and single-user use, not production hosting.

**Before storing important passwords:** review the implementation, test backups and restores, and decide whether the security model fits your needs. No software can guarantee absolute security.

## 🧪 Run tests

Run the included test suite from PowerShell:

```powershell
.\run-tests.ps1
```

Run a subset of tests by name:

```powershell
.\run-tests.ps1 backup
```

The test suite includes checks for password generation, vault operations, encryption handling, authentication, request protections, session locking, backup/restore, and source hygiene.

## 🗂️ Project structure

```text
public/       Front controller and browser assets
src/          Application, authentication, vault, crypto, backup, and database code
config/       App configuration and dedicated PHP server configuration
templates/    PHP page templates
bin/          Environment and database inspection utilities
scripts/      Shared PowerShell environment helpers
tests/        Unit and integration tests
storage/      Runtime database, sessions, logs, and backups (created locally)
start.ps1     Start the local server
stop.ps1      Stop the local server
start.bat     Windows launcher
stop.bat      Windows stop launcher
run-tests.ps1 Test runner
```

## 🧰 Troubleshooting

| Problem | What to try |
|---|---|
| PHP not found | Install PHP 8.3+ or set `VAULTFORGE_PHP` to the full `php.exe` path. |
| Required extension missing | Check that `php_sodium.dll` and `php_pdo_sqlite.dll` are available in your PHP installation. |
| Port 8080 is busy | Run `.\start.ps1 -Port 8081`. |
| Vault is locked after failed attempts | Wait for the displayed lockout period before trying again. |
| Master password forgotten | It cannot be recovered. Use a valid encrypted backup if available. |
| Storage/database error | Check the displayed error and local `storage/` permissions. Back up files before attempting manual recovery. |

## 🤝 Contributing

Issues, bug reports, security feedback, and pull requests are welcome. Please do not include real passwords, vault databases, backup files, session files, or other sensitive data in an issue or pull request.

For security-sensitive reports, avoid publicly posting exploit details or personal vault data.

## 📄 License

No license has been specified in this repository yet. Until a license is added, do not assume that the code is available for reuse, redistribution, or commercial use.

---

**VaultForge — your passwords stay on your PC.**
