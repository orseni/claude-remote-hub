# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [3.2.7] - 2026-05-04

### Fixed
- Restored touch-drag scrolling when direct xterm viewport scrolling is unavailable by falling back to wheel events and tmux copy-mode paging
- Suppressed immediate refocus after fallback scrolling so tmux copy-mode is not exited before the user can read scrollback

## [3.2.6] - 2026-05-03

### Fixed
- Native mobile keyboard handling now reserves explicit bottom space in ttyd instead of relying only on viewport height changes
- Added VirtualKeyboard API support and a touch-focus fallback for browsers that do not report keyboard overlap through `visualViewport`

## [3.2.5] - 2026-05-03

### Fixed
- Removed the recursive resize loop introduced by the mobile native-keyboard viewport adjustment, which could leave ttyd stuck on a blank/gray screen
- Added a minimum viewport height guard before applying mobile keyboard height overrides

## [3.2.4] - 2026-05-03

### Fixed
- Mobile terminal height now tracks `visualViewport` while the native keyboard is open, keeping the prompt/input area visible above the keyboard
- The virtual shortcut bar is hidden while the native keyboard is open to preserve more terminal space

## [3.2.3] - 2026-05-03

### Fixed
- Touch scrolling no longer falls back to tmux copy-mode during finger drags, preventing native mobile keyboard input from getting trapped after scrolling
- Terminal taps now explicitly refocus xterm and exit tmux copy-mode only when it is active, so native keyboard input resumes without requiring the virtual `Esc` key

## [3.2.2] - 2026-05-03

### Fixed
- Custom ttyd mobile layout now targets `#terminal-container` explicitly so touch-scroll controls do not reduce terminal width or cause extra line wrapping
- Embedded ttyd now hides duplicate mobile chrome/padding from the patched direct view, preserving more usable terminal space
- Added resize refits after mobile chrome/keyboard layout changes to keep xterm columns aligned

## [3.2.1] - 2026-05-03

### Changed
- Terminal wrapper now passes hub origin, session, and CSRF token to the custom ttyd page for mobile controls
- CORS/same-origin checks now allow the hub's managed ttyd ports (`7700-7799`) to call CSRF-protected APIs

### Fixed
- Custom ttyd mobile keyboard compatibility after CSRF hardening in 3.2.0

## [3.2.0] - 2026-05-03

### Added
- CSRF-protected POST APIs for starting, stopping, capturing, and controlling sessions
- Unit tests for session validation, path traversal prevention, template escaping, and port collision handling
- GitHub Actions CI for syntax checks, Ruff linting, and unit tests
- Short cache for capturable session discovery to reduce dashboard process scans

### Changed
- Terminal URLs now use HTTP or HTTPS based on the configured hub certificates
- Dashboard actions now call JSON APIs instead of state-changing GET routes
- Session names, route parameters, rendered HTML, and JavaScript literals are validated or escaped consistently
- Session port assignment now resolves hash collisions within the configured port range
- Terminal readiness checks use a lightweight socket probe instead of repeated `lsof` calls
- Mobile/frontend states now expose clearer terminal readiness failures and improved accessibility labels/focus styles

### Fixed
- Path traversal prevention now uses `commonpath` instead of prefix string matching
- Session capture now verifies the target PID against freshly discovered capturable Claude CLI processes

## [3.1.0] - 2026-03-01

### Added
- **Capture running sessions**: detect Claude Code CLI processes already running on the host and capture them into the hub with full conversation history
- **Process discovery**: scan for Claude CLI processes outside hub-managed tmux sessions, resolve their working directory and latest session ID
- **`GET /capture` endpoint**: fork a running conversation into a new tmux + ttyd session using `--resume --fork-session`
- **`GET /api/capturable` endpoint**: JSON list of discoverable CLI sessions
- **Dashboard "Running Sessions" section**: shows capturable processes with project name, PID, and working directory
- **Automatic HTTPS setup**: installer now requests Tailscale certificates during installation

### Changed
- Refactored ttyd startup into reusable `_start_ttyd()` helper
- Installer step count updated (5 → 6) to reflect new HTTPS certificate step
- Added `CLAUDE_REMOTE_HUB_DIR` to LaunchAgent and systemd service environments
- Added `/usr/sbin` to service PATH for `lsof` availability on macOS
- Smaller logo font size for better mobile fit
- README updated: new endpoints in API Reference, version badge, feature description, improved HTTPS docs, line count (~600 → ~1000)

## [3.0.1] - 2025-06-16

### Fixed
- Python < 3.10 compatibility: replaced `str | None` union syntax with `Optional[str]`
- Folder picker fallback when `DEV_ROOT` directory doesn't exist

## [3.0.0] - 2025-06-15

### Added
- **Cross-platform support**: Linux (Ubuntu/Debian, Fedora, Arch) and Windows (WSL2) in addition to macOS
- **Cross-platform installer** (`install.sh`): auto-detects OS and package manager, generates OS-appropriate service files
- **systemd user service** for Linux autostart
- **Android browser support**: generalized mobile detection (was iOS-only)
- **Dependency checker** at startup with per-platform install hints
- **`ss` command fallback** for port detection on Linux (when `lsof` unavailable)
- **Socket-based fallback** for universal port-in-use detection
- **`--uninstall` flag** for `install.sh`
- **Uninstall command** via `claude-remote-hub uninstall`
- Complete open source infrastructure: LICENSE (MIT), CONTRIBUTING.md, CODE_OF_CONDUCT.md, SECURITY.md, ROADMAP.md
- GitHub issue templates (bug report, feature request) and PR template
- `.editorconfig` for consistent formatting

### Changed
- **Complete UI redesign**: modern dark theme with blue-tinted palette, CSS status indicators (replaces emoji), SVG icons, responsive layout
- **All text translated to English** (was Portuguese): UI, code comments, docstrings, error messages, documentation
- **README completely rewritten** in English with marketing intro, multi-OS guides, architecture diagram, API reference
- Binary paths now use `shutil.which()` instead of hardcoded `/opt/homebrew/` paths
- Default `DEV_ROOT` changed from `~/Desenvolvimento` to `~/Projects`
- Install directory configurable via `CLAUDE_REMOTE_HUB_DIR` environment variable
- Type hints added to all public functions
- Virtual keyboard label "Colar" renamed to "Paste"
- Dashboard uses safe DOM methods instead of innerHTML

### Removed
- Hardcoded macOS-only paths
- Portuguese language strings
- Emoji-based status indicators (replaced with CSS)

## [2.3.0] - 2025-06-14

### Changed
- Separated HTML into template files (`templates/hub.html`, `templates/terminal.html`)
- Virtual keyboard reorganized into 2-row layout
- Templates loaded via `_load_template()` with in-memory cache

## [2.2.0] - 2025-06-13

### Added
- Initial public release
- HTTP dashboard for managing Claude Code sessions
- ttyd-based web terminal with custom interface
- tmux session management (create, stop, list)
- Virtual keyboard for mobile (special keys, Ctrl combos)
- HTTPS support via Tailscale certificates
- Folder picker for project directory selection
- Paste support via clipboard API
- macOS LaunchAgent for autostart
