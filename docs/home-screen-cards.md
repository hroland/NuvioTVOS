# Apple TV Home Screen cards

Place Nuvio in the top row of the Apple TV Home Screen. In **Settings → General → Top Shelf**, **Home Screen Cards** is enabled by default and **Home Screen Row** defaults to **Continue Watching**.

The **Featured Card & Posters** layout uses native TVServices sections: the first title gets a landscape featured card, followed by portrait posters for the remaining titles in the selected row. Selecting a card uses the existing content/resume deep links. tvOS controls the final sizing, focus behavior, and refresh timing.

The row picker mirrors the actual cached Home rows, including installed catalog rows such as Trending Movies, Trending Series, and Popular Movies. Open Home once to populate the catalog choices. An unavailable or empty selected row returns no dynamic cards rather than substituting an unrelated catalog. **Custom Source Mix** retains the earlier multi-source controls and all earlier layout styles remain available.

Turning cards off returns the static Top Shelf artwork and cancels future Top Shelf background refresh requests. Saved row/style choices remain available when cards are enabled again. Updates migrate untouched old defaults to Continue Watching cards while preserving customized old layouts.

## Episode and time-left labels

**Settings → Layout → Continue Watching Labels** offers **Always**, **Focused Only** (default), and **Hidden** for in-app episode/status pills and episode-summary text. Title artwork and playback-progress bars remain visible.

**Settings → General → Top Shelf → Episode & Time Labels** controls labels on the Apple TV Home Screen and defaults to off. Top Shelf's sectioned-content API supplies static images without an app focus callback, so a focused-only pill is not offered there. With labels off, the extension requests original card/poster artwork instead of reusing images with baked-in labels; carousel episode/time context is hidden too.

## Signing a personal build

The app and TopShelf extension must share **the same** provisioned App Group. Using `group.$(PRODUCT_BUNDLE_IDENTIFIER)` independently in both targets creates two different groups because the extension bundle ID ends in `.TopShelf`.

The shared project setting `NUVIO_TV_APP_BUNDLE_IDENTIFIER` keeps both entitlement files pointed at the host app's group. To build with a personal bundle identifier:

```sh
# Set DEVELOPMENT_TEAM in the ignored local signing configuration.
cp tvosApp/Configurations/Signing.local.example.xcconfig tvosApp/Configurations/Signing.local.xcconfig
TUIST_NUVIO_TV_BUNDLE_ID=sh.roland.nuviotv tuist generate --path tvosApp --no-open
```

Provision `sh.roland.nuviotv` and `sh.roland.nuviotv.TopShelf` for `group.sh.roland.nuviotv`, including the physical Apple TV in both profiles. Do not commit certificates, provisioning profiles, or the local signing configuration. Keep the same bundle identifier when upgrading to retain app data.

## Validation

The focused `TopShelfHomeCardsTests` cover default selection, disable behavior, exact catalog selection, empty/single-title layouts, persisted settings, compatibility with saved layouts, and migration from untouched defaults. Existing artwork-pruning and write-coordination tests protect shared-feed cleanup and updates. These shared Foundation sources can also be tested on macOS; the complete app and extension should additionally be built for a physical tvOS destination and their signed App Group entitlements checked before deployment.
