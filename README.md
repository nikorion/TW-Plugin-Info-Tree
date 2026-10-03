# TW-Plugin-Info-Tree

**English** · [Français](README.fr.md)

![Status](https://img.shields.io/badge/status-experimental-orange)
![TiddlyWiki](https://img.shields.io/badge/TiddlyWiki-%E2%89%A55.2.0-blue)

A TiddlyWiki plugin that replaces the flat tiddler list in the plugin info panel's **Contents** tab with a hierarchical tree view.

---

## Contents

- [Overview](#overview)
- [Installation](#installation)
- [Development](#development)
- [Files](#files)
- [Version history](#version-history)
  - [v0.1.0](#v010)
- [Credits](#credits)
- [License](#license)

---

## Overview

By default, the "Contents" tab of a plugin's info panel (control panel → Plugins → *pick a plugin* → Contents) lists every tiddler it contains as a flat alphabetical list. For plugins with many tiddlers organised under path-like titles (`plugin-title/modules/foo`, `plugin-title/language/en-GB/`, …), that flat list gets hard to scan.

This plugin overrides the shadow tiddler `$:/core/ui/PluginInfo/Default/contents` to transclude the core `tree` macro instead, rooted at the inspected plugin's own title. Tiddlers are grouped by `/`-separated path segments, the same way tag trees are rendered elsewhere in TiddlyWiki.

> ⚠️ This is a **global** shadow override: once installed, it changes the Contents tab for every plugin's info panel in the wiki — not just for one specific plugin.

[↑](#contents "Back to contents")

---

## Installation

1. Download `TW-Plugin-Info-Tree-Plugin.json` from the [latest release](https://github.com/nikorion/TW-Plugin-Info-Tree/releases/latest)
2. Drag and drop it into your TiddlyWiki (≥ 5.2.0)
3. Save and reload

[↑](#contents "Back to contents")

---

## Development

```
pnpm install
pnpm dev      # dev wiki + hot reload; the URL (random free port) is printed on start
pnpm build    # generates dist/TW-Plugin-Info-Tree-Plugin.json + docs/TW-Plugin-Info-Tree-Wiki.html
```

Sources are in `src/plugin-info-tree/`. The dev wiki (`wiki/`) also loads `tiddlywiki/katex` so there's a plugin with enough nested tiddlers to make the tree view worth looking at while developing.

[↑](#contents "Back to contents")

---

## Files

| File | Role |
|---|---|
| `src/plugin-info-tree/plugin.info` | Plugin metadata |
| `src/plugin-info-tree/plugin-info-override.tid` | Shadow override of `$:/core/ui/PluginInfo/Default/contents` |
| `src/plugin-info-tree/readme.tid` / `licence.tid` / `history.tid` | Plugin info panel tabs |

[↑](#contents "Back to contents")

---

## Version history

### v0.1.0

Initial release.

[↑](#contents "Back to contents")

---

## Credits

Developed with assistance from Anthropic Claude for code, review, and documentation.

[↑](#contents "Back to contents")

---

## License

MIT License — see `LICENSE`

[↑](#contents "Back to contents")
