# patches-and-extensions

Source patches and extensions for apps built from source.

## browsers/helium

`compact-toolbar.patch` changes Helium's compact layout:

- The tabs sit right after the back, forward and reload buttons, before the address bar.
- The address bar is narrower: 280 px in place of 350 px.
- The address bar's text stays left-aligned, also with "Minimal location bar" on.

The other layouts stay as they are. `INTENT.md` says the same in more detail, so the patch can be made again when a new Helium version breaks it.

### Apply it

The patch is source-level and applies to Helium's Chromium tree after Helium's own patches. With [helium-linux](https://github.com/imputnet/helium-linux):

1. Copy `compact-toolbar.patch` to `patches/local/compact-toolbar.patch`.
2. Add `local/compact-toolbar.patch` as the last line of `patches/series`.
3. Build as usual.

Or, in an already patched tree: `patch -p1 -d build/src < compact-toolbar.patch`.
