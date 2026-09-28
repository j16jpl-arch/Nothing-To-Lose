# Nothing to Lose — iOS app wrapper

This wraps the finished web game in a native iOS shell via
[Capacitor](https://capacitorjs.com), so it can be submitted to the App Store.
The game itself is unchanged — this repo only adds the native packaging.
The `ios/` Xcode project is *not* committed here — the GitHub Actions
workflow regenerates it fresh on every run from `www/`, `resources/` and
the two config files below, then builds, signs and uploads it.

## Files that need to exist in this repo

- `www/index.html` — the built game (single self-contained file)
- `resources/icon.png` — the 1024x1024 app icon
- `package.json` — Capacitor dependencies
- `capacitor.config.json` — app id / name / web folder
- `.github/workflows/ios-release.yml` — the build/sign/upload pipeline,
  running on GitHub's free macOS runners. No Mac required, ever.

## One-time setup before the pipeline can actually publish

The workflow needs three secrets so it can sign the app as you. All three
come from an **App Store Connect API key**, which you only need to create
once:

1. Make sure you've enrolled in the **Apple Developer Program** ($99/yr) at
   developer.apple.com.
2. In [App Store Connect](https://appstoreconnect.apple.com) → **Users and
   Access** → **Keys** tab → **Generate API Key**. Give it the **App Manager**
   role.
3. Download the `.p8` key file it gives you (you only get one chance to
   download it — if you miss it, revoke and generate a new one).
4. In this repo on GitHub: **Settings → Secrets and variables → Actions →
   New repository secret**, and add three secrets:
   - `ASC_KEY_ID` — the Key ID shown next to the key you just made
   - `ASC_ISSUER_ID` — the Issuer ID shown at the top of the Keys page
   - `ASC_KEY_P8` — paste the entire contents of the `.p8` file you
     downloaded (open it in Files app / a text editor and copy everything,
     including the `-----BEGIN PRIVATE KEY-----` lines)
5. You'll also need to create the app record itself once in App Store
   Connect (**My Apps → +**), using bundle ID `com.j16jpl.nothingtolose`
   (already set up in this project) — this is what lets the upload step
   know which app to attach the build to.

Once those secrets exist, every push to `main` (or a manual "Run workflow"
from the Actions tab) builds the app fresh and uploads it straight to App
Store Connect / TestFlight — nothing to run locally, ever.

## Updating the game later

To ship a new version of the game itself, replace `www/index.html` with the
new build and push. The workflow handles the rest.
