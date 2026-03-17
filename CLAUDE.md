# CLAUDE.md — nostrkey.browser.plugin.src

## What This Is
NostrKey browser extension — cross-browser Nostr key management, encrypted vault, and identity layer. The core codebase that also powers the iOS and Android apps.

## Ecosystem Position
NostrKey is **the hand that holds the baseball card**. It manages your private keys, signs events, encrypts data, and connects you to your NostrKeep relay and npub.bio identity. Free, open source (MIT), forked from ursuscamp/nostore.

## Current Version
v1.5.9 (build 5.5.0) — Chrome/Firefox live. iOS + macOS Safari resubmitted to App Store 2026-03-17.

## Tech Stack
- Vanilla JS (Alpine.js was removed)
- esbuild bundler
- Tailwind CSS
- nostr-crypto-utils for protocol operations
- Chrome Manifest V3

## Build Commands
```bash
npm install
npm run build           # Safari: Tailwind + esbuild
npm run build:chrome    # Chrome → distros/chrome/
npm run build:all       # Both targets
npm run build:all:prod  # Both, minified
npm run watch           # Watch mode (JS, Safari)
npm run watch-tailwind  # Watch mode (CSS)
```

**Important:** After changing extension source code, run `npm run build` and commit the updated `distros/safari/` along with your source changes. Xcode Cloud needs `distros/safari/` in the repo.

## Chrome Dev
1. `npm run build:chrome`
2. `chrome://extensions/` → Developer mode → Load unpacked → `distros/chrome/`

## NIPs Implemented
NIP-01, NIP-04 (deprecated), NIP-07, NIP-19, NIP-44, NIP-46, NIP-49, NIP-78

## Key Features
- NIP-07 `window.nostr` signing
- NIP-46 nsecBunker (remote signing)
- NIP-44 encryption (ChaCha20-Poly1305)
- Encrypted .md vault + API key vault (NIP-78)
- Multi-profile with per-site permissions
- Master password with auto-lock
- Cross-device sync via storage.sync
- WCAG AA accessibility

## Repo Structure
```
src/                    # Extension source (JS, CSS, HTML)
dev/apple/              # Xcode project (Safari/iOS wrapper)
dev/qa/                 # QA automation (screenshot capture/resize)
distros/safari/         # Safari build output (TRACKED in git for Xcode Cloud)
distros/chrome/         # Chrome build output (gitignored)
distros/firefox/        # Firefox build output (gitignored)
docs/                   # Website (nostrkey.com), privacy, terms
docs/python.html        # Python SDK docs page (nostrkey.com/python)
docs/test.html          # Extension test page (nostrkey.com/test)
docs_project_info/      # Project docs (testing, submission, vision)
ci_scripts/             # Xcode Cloud CI scripts
build.js                # esbuild config
tailwind.config.js      # Tailwind config
```

## Xcode Cloud
- `ci_scripts/ci_post_clone.sh` exists as backup but `distros/safari/` is tracked in git, so Xcode Cloud builds work without running it.
- Builds auto-trigger on push to `main`.
- Archives both iOS and macOS targets.

## Safari / App Store Build
```bash
# Archive for macOS
xcodebuild archive -project dev/apple/NostrKey.xcodeproj \
  -scheme "NostrKey (macOS)" -configuration Release \
  -archivePath dev/qa/archives/NostrKey-macOS.xcarchive

# Archive for iOS
xcodebuild archive -project dev/apple/NostrKey.xcodeproj \
  -scheme "NostrKey (iOS)" -configuration Release \
  -destination "generic/platform=iOS" \
  -archivePath dev/qa/archives/NostrKey-iOS.xcarchive

# Upload via Xcode Organizer → Distribute App → App Store Connect
```

**Bundle IDs** (must match across platforms for unified listing):
- App: `com.nostrkey`
- Extension: `com.nostrkey.Extension`
- Team: `H48PW6TC25`

**macOS Entitlements** (Guideline 2.4.5(i)):
- `com.apple.security.app-sandbox` — required
- `com.apple.security.network.client` — outgoing WebSocket connections
- Do NOT add `network.server` — Apple rejects it (NostrKey is client-only)

## Architecture
Extension uses background service worker + sidepanel UI. Mobile apps (iOS/Android) wrap this in dual-WebView architecture with native bridges (IOSBridge.swift / AndroidBridge.kt).

## Conventions
- Vanilla JS, no frameworks
- kebab-case file names
- Chrome Web Store zips go in `distros/` folder
- Xcode project lives at `dev/apple/NostrKey.xcodeproj`
- WCAG AA contrast, aria-labels, reduced-motion support

## Analytics
Plausible (privacy-friendly, cookieless) on all public docs pages. Script: `pa-IB1d6aIMpkIZgRxSc6Med.js`.

## Related Repos
- `nostrkey.app.OC-python.src` — Python SDK for AI entities (v0.2.0, `pip install nostrkey`)
- `nostrkey.app.ios.src` — iOS app (WKWebView wrapper)
- `nostrkey.app.android.src` — Android app (WebView wrapper)
- `nostrkey.bizdocs.src` — business strategy docs
- `npub-bio-landingpage` — npub.bio (uses NostrKey for NIP-07 connect)
- `nostrkeep.srvr.relay.src` — NostrKeep relay (NostrKey points keys here)
