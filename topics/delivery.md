# Delivery Format: Universal Checklist for HTML / IPA / APK / EXE

> One design logic, different shells. **Design rules are platform-agnostic; navigation, touch, permissions, build, and acceptance land per target platform.**
> Don't stuff a web page into a mobile shell, and don't force desktop controls onto a phone.

## 1. Target Matrix

| Target | Artifact | Tech stack | Key differences |
| --- | --- | --- | --- |
| Web | Static site / SPA / PWA | HTML/CSS/JS, React, Vue, Svelte | Keyboard, responsive, browser compatibility, first-paint performance |
| iOS | `.ipa` | SwiftUI / UIKit / Flutter / RN | Safe area, Dynamic Type, back gesture, signing |
| Android | `.apk` / `.aab` | Compose / XML / Flutter / RN | 48dp, system back, font scaling, signing |
| Windows | `.exe` / installer | Tauri / Electron / .NET / WPF | High DPI, window resizing, shortcuts, signing |
| Cross-platform | Multiple artifacts | Flutter / React Native / Tauri | Shared logic; controls and navigation adapted separately |

## 2. Web / HTML

**Must**
- `<meta name="viewport" content="width=device-width, initial-scale=1">`; no `user-scalable=no`.
- Three breakpoints: 390 / 768 / 1440; no horizontal overflow at 280px.
- Content area `max-width: 1280px`, body text `65ch`.
- Keyboard can complete the primary flow; focus is visible; Esc closes overlays.
- Mobile `100dvh` + `env(safe-area-inset-*)`; inputs `font-size ≥16px`.
- Images `srcset` + `loading="lazy"` + fixed aspect ratio; fonts `font-display: swap`.
- Inline critical above-the-fold CSS; defer non-critical JS; virtualize long lists.

**Build and Acceptance**
```bash
npm run build          # outputs dist/
npx serve dist         # verify via a local server; don't double-click file://
```
- Walk through on a real device (iOS Safari / Android Chrome), not just desktop.
- Turn off the network to check the offline state; enable reduce-motion to check degradation; use 200% text size to check truncation.
- PWA: manifest, Service Worker, offline page, install prompt; don't cache auth state.

## 3. iOS `.ipa`

**Must**
- Safe area: `safeAreaInsets` / SwiftUI `.safeAreaInset`; leave room for the home indicator under the bottom action bar.
- Dynamic Type: use `Font.body/.title` or `UIFontMetrics`; no truncation at 200%.
- Navigation: Tab Bar ≤5 items, back chevron + gesture; sheets dismiss by pull-down.
- Touch ≥44×44pt; haptics via `UIImpactFeedbackGenerator` / `.sensoryFeedback`.
- Reduce motion: `UIAccessibility.isReduceMotionEnabled`.
- Permission copy states the purpose clearly; explain before the first request.
- App icon at all sizes; launch screen; privacy manifest (PrivacyInfo.xcprivacy).

**Build and Acceptance**
```bash
xcodebuild -scheme App -configuration Release \
  -archivePath build/App.xcarchive archive
xcodebuild -exportArchive -archivePath build/App.xcarchive \
  -exportPath build/ipa -exportOptionsPlist ExportOptions.plist
```
- Needs a signing certificate and provisioning profile; CI uses a `macos-26` runner + XcodeGen to generate the project, then exports an unsigned/self-signed IPA.
- After installing on a real device (TestFlight / Sideloadly / AltStore), walk the primary flow; the simulator doesn't count.
- Check: small screen (SE), largest text size, dark mode, landscape (if supported), weak network, background restore.

## 4. Android `.apk` / `.aab`

**Must**
- Material 3: TopAppBar, NavigationBar, FAB, BottomSheet; don't mix in iOS controls.
- Touch ≥48×48dp; text in `sp`; no truncation at 200% system font size.
- System back/predictive back works; don't intercept back for "press again to exit" unless necessary.
- Edge-to-edge: `WindowCompat.setDecorFitsSystemWindows(false)` + insets.
- Request permissions on demand, with a degraded path after denial; don't ask for everything on launch.
- Dark theme uses Material semantic colors; dynamic color (Material You) optional.
- Reduce animation: switch instantly when the system animation scale is 0.

**Build and Acceptance**
```bash
./gradlew assembleRelease   # APK
./gradlew bundleRelease     # AAB (for Google Play publishing)
```
- Configure signingConfig; don't commit the keystore.
- Verify: small screen (360dp), 200% font, dark mode, RTL, back gesture, offline, upgrade install.
- Native libraries: mind 16 KB page size (Android 15+); publish with AAB.

## 5. Windows `.exe`

**Must**
- High DPI: `PerMonitorV2`; at 125% / 150% / 200%, no blur and no truncation.
- Minimum window size; resizable; remember window position (optional).
- Keyboard: Tab order, Enter for the primary action, Esc to cancel, common shortcuts.
- Localize the title bar/menus/installer; RTL follows the system.
- File paths, permissions, proxy, and offline need clear errors.
- Code signing to avoid SmartScreen warnings; clean install/uninstall/upgrade paths.

**Build and Acceptance**
```bash
# Tauri
npm run tauri build
# Electron
npx electron-builder --win
# .NET
dotnet publish -c Release -r win-x64 --self-contained
```
- Install once on a clean Windows VM; test uninstall residue, upgrade overwrite, and launch without network.
- Electron: watch WebView/Chromium size; Tauri: watch the WebView2 runtime dependency.

## 6. Cross-Platform (Flutter / React Native)

- Reuse the logic and data layers; adapt navigation, controls, permissions, and haptics separately.
- Don't use one set of pixel sizes: keep iOS pt / Android dp / Web px separate.
- Fonts: don't ship a Latin-only font for Chinese/Arabic.
- Animation: reduce particles/blur/parallax on low-end Android; respect the system animation scale.
- Acceptance: run each platform separately; "one Flutter codebase" is no excuse to test only one platform.

## 7. Universal Delivery Checklist

- [ ] Artifact installs/opens from a clean environment, with no dependency on local caches
- [ ] Version number, build number, icon, and launch image are correct
- [ ] Primary flow verified on a real device; not just simulator/browser
- [ ] Offline, weak network, permission denial, and empty data all have states
- [ ] Dark mode, 200% text size, and reduce-motion pass
- [ ] Install/upgrade/uninstall leave no residue and lose no data
- [ ] Crashes/errors are logged; stack traces aren't dumped on users
- [ ] Installer size within budget; first-screen load acceptable
- [ ] Privacy policy, permission descriptions, and third-party SDK list complete (required for publishing)

## 8. Design Rules Mapped to Platforms

| Design rule | Web | iOS | Android | Windows |
| --- | --- | --- | --- | --- |
| Touch ≥44 | 44px | 44pt | 48dp | 32–40px (mouse) |
| Navigation | Sidebar/top bar/breadcrumb | Tab Bar / NavigationStack | NavigationBar / Drawer | Menu bar/sidebar |
| Back | Browser/breadcrumb | Gesture + chevron | System back | Menu/shortcut |
| Font scaling | 200% browser | Dynamic Type | sp + system font size | System scaling |
| Reduced motion | `prefers-reduced-motion` | `isReduceMotionEnabled` | Animation scale 0 | System animation setting |
| Safe area | `env(safe-area-inset-*)` | `safeAreaInsets` | WindowInsets | Window border |
| Theme | `prefers-color-scheme` | System appearance | System dark / Material You | System theme |

At acceptance, first ask "which shell is this," then pick the matching checklist; don't apply Web's 44px to Android, and don't apply Android's 48dp to a desktop mouse.
