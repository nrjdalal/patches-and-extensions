# Helium "compact toolbar" patch: what it does

This file is a rebuild guide for an AI model. When a new Helium version breaks `compact-toolbar.patch`, a model reads this file and makes the same change again in the new source tree. The behaviour below is the contract; the old patch is only a hint.

## The behaviour

- Compact layout (`helium.browser.layout` = 1) puts the toolbar row in this order, left to right:
  `[back][forward][reload]` `[tabs]` `[address bar]` `[pinned toolbar icons]` `[extensions]` `[⋮ menu]`.
  Stock Helium puts the address bar before the tabs; this patch puts the tab strip right after the navigation buttons.
- The compact address bar's preferred width is **280** (stock is 350).
- In the compact layout the address bar's text and placeholder stay **left-aligned**, at rest and while typing. Stock centers them at rest when Helium's "Minimal location bar" setting (`helium.browser.minimal_location_bar`) is on; the minimal bar's shortened URL stays, and the bar's position and width are untouched.
- Classic, Vertical and Dynamic layouts behave exactly as stock. Only the compact row changes.

## Where it lives today

- `chrome/browser/ui/views/toolbar/toolbar_view.cc`:
  - `kPreferredCompactWidth`: the compact address bar's width constant.
  - `ToolbarView::AttachTabStripRegionView` (or whatever now inserts the tab strip region into the toolbar in compact mode): the tab strip's insertion index, which is the location bar's index (`*location_bar_index`), so the tabs land before the address bar, not after it (`+ 1`).
- `chrome/browser/ui/views/omnibox/omnibox_view_views.cc`, `OmniboxViewViews::UpdateSteadyStateTextAlignment`: `center_text` turns false when `HeliumLayoutStateController::From(location_bar_view_->browser())->IsCompactLayout()`.
- It sits on Helium's own `patches/helium/ui/layout/compact.patch` and `toolbar-layout.patch`, which add the compact mode it changes, and `minimal-location-bar.patch`, which adds the centering.

## The model's output

1. Edit the new Chromium tree (the `src` folder, with Helium's own patches applied) so the behaviour above holds, touching as few lines as you can, in the same place the compact row is built.
2. Keep the comment marker `// Local patch (compact toolbar)` on each changed line, so a check can find it.
3. Done when `rg -n "Local patch \(compact toolbar\)" chrome/browser/ui/views` finds the marker on each changed line and the edit matches the behaviour above. Leave builds to the caller: it compiles the changed files, builds Helium and screenshots the compact row with the compact layout and the minimal location bar on, so a centered hostname fails the check.

A change outside the compact row and the compact address bar's text alignment is rejected.
