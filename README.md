# Markdown Rich Review for GitHub Pull Requests

> **Quadric fork.** This is a fork of [sabbour/md-review-extension](https://github.com/sabbour/md-review-extension)
> with fixes for spec-style markdown PRs:
> - Click-to-source now resolves correctly for paragraphs containing inline LaTeX (`$...$`).
> - Multi-line (soft-wrapped) paragraphs route clicks to the specific source line clicked,
>   not just the paragraph's first line.
> - Selected-line highlight is dark-theme readable (translucent blue instead of pale yellow).
>
> **Install (Quadric coworkers):**
> 1. `git clone git@github.com:thomassng/md-review-extension.git ~/extensions/md-review-extension`
> 2. Open `chrome://extensions` → toggle **Developer mode** on.
> 3. Click **Load unpacked**, point at `~/extensions/md-review-extension`.
> 4. To update later: `cd` to the directory, `git pull`, then click the reload icon on the extension's card.
>
> Updates from upstream sabbour land via `git fetch upstream && git merge upstream/main`. Ping Thomas if you need help.

A Chrome / Microsoft Edge extension (MV3) that enhances the GitHub Pull Request **Files changed** view for Markdown files.

![License](https://img.shields.io/badge/license-MIT-blue)

## Features

- **Rich diff enhancement** — When you switch a `.md` file to GitHub's rich diff view, the extension overlays line numbers, comment indicators, and click-to-source navigation.
- **Line-level comment indicators** — Lines with existing review comments show a chat-bubble icon with superscript count in the right margin, and a permanent dashed outline.
- **Click-to-source** — Click any element in the rich preview to jump to the corresponding line in the source diff, with automatic expansion of collapsed sections.
- **Back to rich view** — A header button lets you switch back from source diff to rich preview in one click. It only appears when you're in source diff mode.
- **Comment bar** — A summary bar at the top of each rich diff shows all commented lines with quick-jump badges.
- **Toggle on/off** — Click the floating status badge (with extension icon) to pause or resume the extension. Pausing fully removes all injected DOM elements.
- **Source line highlighting** — Selected source lines are persistently highlighted with automatic retry for lazy-loaded diffs.
- **Comment activity tracking** — Starting a review or adding comments reflects back in the rich diff indicators in real time.

## Installation

### From source (developer mode)

1. Clone this repository:
   ```bash
   git clone https://github.com/<your-username>/md-review-extension.git
   cd md-review-extension
   ```

2. Open your browser's extension page:
   - **Edge**: `edge://extensions`
   - **Chrome**: `chrome://extensions`

3. Enable **Developer mode**.

4. Click **Load unpacked** and select the project directory.

5. Navigate to any GitHub PR → **Files changed** tab.

### From packaged zip

1. Download the latest `.zip` from [Releases](../../releases).
2. Unzip to a folder.
3. Load unpacked from that folder (same steps as above).

## Packaging

To create a distributable `.zip` locally:

```bash
npm run package
```

This outputs `dist/markdown-rich-review-<version>.zip`, ready for Chrome Web Store or Edge Add-ons upload.

## CI / CD

A GitHub Actions workflow ([`.github/workflows/release.yml`](.github/workflows/release.yml)) automates packaging and releasing:

| Trigger | What happens |
|---|---|
| Push to `main` | Packages the extension and updates a rolling `latest` pre-release with the `.zip` ([`ci.yml`](.github/workflows/ci.yml)) |
| Push a tag `v*` (e.g. `v2.1.0`) | Packages the extension, creates a GitHub Release with the `.zip` attached and auto-generated release notes ([`release.yml`](.github/workflows/release.yml)) |
| Manual dispatch (`workflow_dispatch`) | Packages the extension and uploads the `.zip` as a build artifact |

### Creating a release

1. Update the version in `manifest.json` and `package.json`.
2. Commit and push:
   ```bash
   git add -A && git commit -m "Release v2.1.0"
   git tag v2.1.0
   git push origin main --tags
   ```
3. The pipeline will automatically build and publish the release with the installable `.zip`.

## File structure

```
md-review-extension/
├── manifest.json              # MV3 extension manifest
├── content-script.js          # Main content script — all extension logic
├── utils/
│   └── domHelpers.js          # Shared DOM utility functions
├── styles/
│   └── reviewPane.css         # All extension styles
├── icons/
│   ├── icon16.png             # Extension icon 16×16
│   ├── icon32.png             # Extension icon 32×32
│   └── icon64.png             # Extension icon 64×64
├── scripts/
│   └── package.mjs            # Packaging script for distribution
├── .github/
│   └── workflows/
│       ├── ci.yml             # Latest build on every push to main
│       └── release.yml        # Versioned release on tag push
├── package.json
├── LICENSE
└── README.md
```

## How it works

The extension runs as a content script on `github.com/*` pages. On PR Files changed pages, it:

1. **Detects** `.md` files among the diff containers (via embedded payload metadata and DOM inspection).
2. **Enhances** the rich diff view when the user switches to it — adding click handlers, line numbers, and comment indicators.
3. **Fetches** raw file content (using same-origin credentials) to build a line map for accurate source-position mapping.
4. **Navigates** to the source diff on click, expanding collapsed sections and highlighting the target line.

No GitHub API tokens or special permissions are required. The extension uses only the browser session already authenticated with GitHub.

## License

[MIT](LICENSE)
