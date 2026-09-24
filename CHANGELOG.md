# Changelog

All notable changes to `@arraypress/waveform-bar-vue` are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).


## [Unreleased]

## [0.4.0] — 2026-09-24

### Added

- **`waveformGradient` config key** (`'vertical' | 'horizontal' |
  'diagonal'`). The bar has forwarded it to the embedded player since 1.8.0,
  but it was missing from `WaveformBarConfig`, so TypeScript rejected a
  working option.
- **`barRadius` config key** (`number | null`) — rounded bar caps in px,
  `0` = square, `null` = the player's default. Requires
  `@arraypress/waveform-bar` 1.12.0+; older bars ignore it.

### Changed

- **Peer floors raised:** `@arraypress/waveform-bar` `^1.10.0` → `^1.11.0`
  and `@arraypress/waveform-player` `^1.7.2` → `^1.8.0`. `WaveformBarConfig`
  has declared `crossOrigin` since 0.3.0, but the bar only accepts it from
  1.11.0; and the bar drives `audioMode: 'external'` inline players, which
  crashed before core player 1.8.0. The optional `barRadius` / working
  `showTime` (bar 1.12.0) don't raise the floor — older bars ignore them.

### Fixed

- **`waveformColor` / `progressColor` accept gradient stop arrays.** They
  were typed `string | null`, but the bar forwards them verbatim to a player
  that takes `string | string[] | null` — `['#fafafa', '#71717a']` works at
  runtime and now typechecks.
- **`showTime` doc comment** notes that `false` actually hides the time
  display from `@arraypress/waveform-bar` 1.12.0 (earlier bars ignored it).

## [0.3.0] — 2026-07-22

### Added

- **`crossOrigin` config key.** Added to `WaveformBarConfig` and forwarded
  verbatim to `window.WaveformBar.init()`. Requires
  `@arraypress/waveform-bar@^1.11.0` for it to reach the embedded player.

## [0.2.0] — 2026-07-05

### Added

- Accept the new localizable player-string keys — `seekValueText`,
  `playPauseLabel`, `speedLabel`, `artworkAlt`, and `unknownTrackText` — in
  the bar config; they are forwarded to the embedded player. Requires
  `@arraypress/waveform-bar@^1.10.0`.

## [0.1.0] — Unreleased

Initial release.

### Added

- `<WaveformBar>` — singleton mount for the persistent bottom bar. Render
  once in your root layout. On mount it dynamically imports
  `@arraypress/waveform-bar` (SSR-safe) and calls
  `window.WaveformBar.init(config)`; re-inits when the config's structural
  shape changes (compared via `JSON.stringify`, so a fresh object with the
  same shape doesn't churn); relocates the bar element into a persist host
  `<div>`; and calls `destroy()` on unmount. `config`, `persist`, `hostId`
  props; `class` / `style` fall through to the host (base class `wb-host`).
- `<WaveformBarTrigger>` — polymorphic (`as="button" | "a" | "div" |
  "span"`, default `button`) click trigger that emits the `data-wb-*`
  attribute contract the core library scans for. Maps track-data props
  (`url`, `id`, `title`, `artist`, `album`, `artwork`, `link`, `duration`,
  `bpm`, `musicalKey`, `meta`, `waveform`, `markers`, `favorited`,
  `inCart`) to attributes (arrays JSON-encoded; absent props emit no
  attribute). `mode="play" | "queue"`, `href` (for `as="a"`),
  `noDefaultIcons`. Renders default play/pause SVGs unless slot content is
  provided. Auto-generates an `aria-label` from `title`. `class`, native
  listeners (`@click`), and other attributes fall through; the base class
  `wb-icon-swap` is always applied.
- No lifecycle callback props — the bar dispatches every state change as a
  bubbling `waveformbar:*` `CustomEvent`; listen with `addEventListener`.
  This keeps callbacks from forcing a bar re-init (matching the React
  wrapper).
- Public types mirroring the core surface: `WaveformBarConfig`,
  `WaveformBarProps`, `WaveformBarTriggerProps`, `WaveformBarTrackData`,
  `WaveformBarMarker`, `WaveformBarActions`, `WaveformBarAction`,
  `WaveformBarTheme`, `RepeatMode`, `TriggerMode`, `WaveformStyle`.
- Dual ESM + CJS build via `tsup` with `.d.ts` for both. Vue + the core
  libraries are peer dependencies.
- Vitest test suite (jsdom + `@vue/test-utils`, 13 tests) covering the
  singleton's host render + `init` / re-init / `destroy` lifecycle, and the
  trigger's `data-wb-*` contract, polymorphism, modes, default-icon
  handling, class merge, and click forwarding.
