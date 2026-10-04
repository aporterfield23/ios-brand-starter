# Brand iOS App — Developer Handoff

Approved version: (tag, e.g. brand-v1.0)
Demo: (Vercel production URL)
Figma: (file link, page "Screens")

## Design tokens
Generated from `tokens/tokens.json` by `npm install && npm run tokens` (Node 22+). Do not edit the generated files by hand.

- `ios/DesignSystem/Colors.swift`: `Color.Brand.*`. Colours iOS owns (backgrounds, label colours, separator) are references such as `Color(uiColor: .systemBackground)` and `Color.primary`, so they follow iOS in dark mode and future releases. Brand colours point to the asset catalog.
- `ios/Assets.xcassets`: brand-owned colours only, each with light, dark and Increase Contrast appearances (the stronger variants reach at least 7:1 against every background and surface; colours already at 7:1 keep their value). Add the folder to the app target (or merge its colorsets into the app's catalog).
- `ios/Assets.xcassets/AccentColor`: the app tint, with the same values as `Color.Brand.accent`. New Xcode projects already use the asset named AccentColor (build setting "Global Accent Color Name"), so controls, toggles and links pick up the brand colour with no extra code. Remove the template's own AccentColor if it has one.
- Every token build is checked on GitHub with Apple's tools: the Swift is type-checked for iOS 17 and the asset catalog is compiled (job `check-ios` in `.github/workflows/tokens.yml`). A green run means the generated files compile.
- `ios/DesignSystem/Typography.swift`: `Font.Brand.*` built on iOS text styles (`.body`, `.largeTitle` and so on), so Dynamic Type works. A custom brand font uses `Font.custom(_:size:relativeTo:)`. `Tracking.*` holds letter spacing; apply with `.tracking()`.
- `ios/DesignSystem/Spacing.swift`: `Spacing.*` and `Radius.*` in points, 1:1 with Figma.
- `ios/DesignSystem/kit-tokens.json`: manifest mapping each Figma token to its Swift expression and colorset, for syncing changes back to Figma.
- `ios-system-map.json` decides which tokens are system-owned. Edit it to move a token between system and brand.

## Fonts
Families set to "SF Pro" use the system font and need no files; never bundle SF Pro. Any other brand font needs its files in the app.

- Font files go in `ios/Fonts/`, unmodified from the font's official release, with its license file (for Google Fonts, `OFL.txt`). Use the official static files (one per weight, e.g. `Brand-Bold.ttf`, `Brand-SemiBold.ttf`), not a variable font: a variable font's default instance is often a different width or weight, so iOS may not match the family name. Don't make your own static cuts if the license reserves the font name (most OFL fonts do).
- Include at least the weights `Typography.swift` uses (check its `.weight(...)` calls), plus Regular if the app needs it elsewhere.
- Setup: drag the files into Xcode with the app target ticked, then list each file name in Info.plist under "Fonts provided by application" (`UIAppFonts`):

  ```xml
  <key>UIAppFonts</key>
  <array>
      <string>Brand-Bold.ttf</string>
      <string>Brand-SemiBold.ttf</string>
  </array>
  ```

- `Typography.swift` asks for the family name with `Font.custom(_:size:relativeTo:)` and sets the weight, so iOS picks the matching file. Check a title on a device: if it shows in SF Pro, the font isn't registered (check target membership and the Info.plist names). Don't rename the family token in Figma to fix it; the website loads the font by that name. Record each file's PostScript name in this section in case a developer wants to reference a face directly.
- Dynamic Type still works: each style uses `relativeTo:` an iOS text style. Check long titles at the largest accessibility sizes.
- The website loads brand fonts from Google Fonts (`web/src/fonts.css`), so the files in `ios/Fonts/` are for the app only.

## Website
The same tokens style a website. Everything is in `web/`:
- `web/src/tokens.css` and `web/src/tokens-dark.css`: CSS variables (`var(--color-accent)`, `var(--space-md)`, `var(--type-body-size)` and so on). Dark mode follows the visitor's system setting; set `data-mode="light"` or `data-mode="dark"` on any element to force a mode. Stronger colours apply automatically when the browser asks for more contrast.
- `web/src/fonts.css`: loads the brand fonts from Google Fonts. Font variables already include a system-font fallback; SF Pro is licensed for Apple platforms only, so the web uses the device's system font (SF on Apple devices).
- `web/tailwind.preset.js`: a Tailwind theme pointing at the CSS variables (Tailwind 3: `presets: [...]`; Tailwind 4: `@config`). Classes like `bg-accent`, `p-md`, `rounded-lg`, `font-display` and `text-body` switch with light, dark and contrast settings, so colour needs no `dark:` variants.
- Text sizes are fixed pixel values from the iOS default size; use them with responsive units if the site needs to scale.

## Screens in scope
1. Launch 2. Home 3. Detail 4. Settings

## Accessibility
- WCAG AA contrast checked in the Brand Token Spec
- Increase Contrast supported: brand colours switch to stronger variants (at least 7:1) automatically, in the app and on the web (prefers-contrast: more)
- 44x44 pt minimum tap targets

## Contact
Alan Porterfield
