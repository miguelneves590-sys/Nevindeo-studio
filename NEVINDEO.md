# Nevindeo

Nevindeo is a modified fork of **Concat** (https://github.com/jub0t/Concat),
by Jareer and the Concat contributors, licensed **AGPL-3.0-or-later**. The
`LICENSE`, `THIRD_PARTY_NOTICES.md`, the SPDX headers and the copyright notices
of the original are kept unchanged. This fork is a modified version and, as
`TRADEMARK.md` asks, it carries its own name and icon. Because it is AGPL
software, anyone who receives or uses a build must be able to get its source.

## What changed in this fork

| Area | Files | Change |
|---|---|---|
| Android name | `src/crates/concat-android/Cargo.toml` | `label` and `apk_name` are now `Nevindeo` (the APK is `Nevindeo.apk`) |
| Export folder | `concat-android/java/ConcatFiles.java`, `concat-android/src/lib.rs` | finished exports go to `Movies/Nevindeo` instead of `Movies/Concat` |
| Project folder | `concat/src/panes/start.rs` | default folder for new projects on Android is `Nevindeo` (the test is updated) |
| Window title | `concat/ui/app.slint`, `concat/src/platform.rs` | `Nevindeo` |
| Violet theme | `concat/src/prefs.rs`, `concat/ui/theme/theme.slint` | the existing Violet accent is the default on a fresh install; a saved choice is kept; tests updated |
| Texts | `concat/locales/*.json` (25 languages) | "Concat" in visible strings is now "Nevindeo" (placeholders untouched). Left as they were: names of upstream services (Concat sounds, mirror, API) and strings written in non-Latin transliteration |
| Icon | `src/crates/concat-android/res/mipmap-*/ic_launcher.png`, `assets/icons/nevindeo_logo_*.png`, `assets/nevindeo/` | provisional violet icon with an "N" (no official art was supplied); the Concat artwork is left in `assets/` |
| Start screen (phone) | `src/crates/concat/ui/start.slint`, `locales/en.json`, `locales/pt-BR.json` | new header with the app icon, name and tagline on a violet wash; round Settings button; taller New project tile; projects as cards with a gap. New text key `start.tagline` (other languages fall back to English) |
| CI | `.github/workflows/nevindeo-android.yml` (new), `mobile.yml`, `scripts/ci/smoke-android.sh` | Android-only arm64 workflow; `mobile.yml` and the smoke script follow the new APK name and log line |

Deliberately **not** changed: the package id `app.concat.editor` (it is also the
JNI class name `app.concat.editor.ConcatFiles`, the data-folder identifier in
`concat-host/src/dirs.rs`, and used by the smoke script), the crate names, the
`concat.json` project format, the engine, the timeline, the export pipeline and
every editing feature. Consequence: Nevindeo and the official Concat share one
package id, so they cannot be installed side by side. If you want both, change
the id in all four places above in one commit and rebuild.

## Building the APK (arm64-v8a)

Nothing was compiled when these changes were made (see the test report in the
hand-off message). Build in an environment that has the Android SDK + NDK and
network access:

1. Push this tree to a GitHub repository.
2. Actions tab -> "Nevindeo Android (arm64)" -> Run workflow.
3. Download the artifact `Nevindeo-android-arm64` (`Nevindeo-<version>-android-arm64.apk`).
   The first run builds FFmpeg from source and takes a long time; later runs
   reuse the cache. The workflow checks that the APK exists, holds
   `lib/arm64-v8a/`, is labelled Nevindeo and has a valid signature.
4. Enable "install unknown apps" on the phone and open the APK.

Local build: install Rust 1.93.1 (pinned in `src/rust-toolchain.toml`), the
`aarch64-linux-android` target, `cargo-apk`, cmake and libclang, then from the
repository root run the same three scripts the workflow runs:
`src/scripts/ffmpeg-mobile.sh aarch64-linux-android`,
`src/scripts/sherpa-mobile.sh aarch64-linux-android`, and in `src/`:
`eval "$(scripts/mobile-env.sh aarch64-linux-android)" && cargo apk build --release -p concat-android --target aarch64-linux-android`.
The APK lands in `src/target/release/apk/Nevindeo.apk`.

## Known limits

* The Settings page still reads release information from the upstream Concat
  repository (`concat-host/src/updates.rs`). On Android it cannot install a
  package (the app reports a store-managed install), so nothing is replaced,
  but the version line may mention Concat releases. Point `REPO` and
  `RELEASES_URL` at your own repository, or hide that row, when you publish.
* The launcher icon is provisional.
* The UI is portrait-only on Android, as upstream.
