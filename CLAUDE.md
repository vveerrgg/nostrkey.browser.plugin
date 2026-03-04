# CLAUDE.md — nostrkey.browser.plugin.src

## What This Is
NostrKey browser extension — cross-browser Nostr key management, encrypted vault, and identity layer. The core codebase that also powers the iOS and Android apps.

## Ecosystem Position
NostrKey is **the hand that holds the baseball card**. It manages your private keys, signs events, encrypts data, and connects you to your NostrKeep relay and npub.bio identity. Free, open source (MIT), forked from ursuscamp/nostore.

## Current Version
v1.5.6 — Live on Chrome Web Store, Android (Google Play). iOS + macOS Safari submitted to App Store (pending review, 2026-03-03).

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
dev/qa/screenshots/     # App Store screenshots (gitignored except HOWTO.md)
  HOWTO.md              # Full screenshot capture procedure
  macos/                # 2560x1600 (8 screenshots)
  iphone/               # 1284x2778 (8 screenshots)
  ipad/                 # 2048x2732 (8 screenshots)
distros/                # Build output (gitignored)
docs/                   # Website, privacy, terms
docs/test.html          # Extension test page (nostrkey.com/test)
docs_project_info/      # Project docs (testing, submission, vision)
build.js                # esbuild config
tailwind.config.js      # Tailwind config
```

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

## Architecture
Extension uses background service worker + sidepanel UI. Mobile apps (iOS/Android) wrap this in dual-WebView architecture with native bridges (IOSBridge.swift / AndroidBridge.kt).

## Conventions
- Vanilla JS, no frameworks
- kebab-case file names
- Chrome Web Store zips go in `distros/` folder
- Xcode project lives at `dev/apple/NostrKey.xcodeproj`
- WCAG AA contrast, aria-labels, reduced-motion support

## Related Repos
- `nostrkey.app.ios.src` — iOS app (WKWebView wrapper, v1.1.1)
- `nostrkey.app.android.src` — Android app (WebView wrapper, v1.1.1)
- `nostrkey.bizdocs.src` — business strategy docs
- `npub-bio-landingpage` — npub.bio (uses NostrKey for NIP-07 connect)
- `nostrkeep.srvr.relay.src` — NostrKeep relay (NostrKey points keys here)
