# TW-Plugin-Info-Tree

**English** · [Français](README.fr.md)

![Status](https://img.shields.io/badge/status-experimental-orange)
![TiddlyWiki](https://img.shields.io/badge/TiddlyWiki-%E2%89%A55.2.0-blue)

A TiddlyWiki plugin that replaces the flat tiddler list in the plugin info panel's **Contents** tab with a hierarchical tree view.

## Overview

By default, the "Contents" tab of a plugin's info panel (control panel → Plugins → *pick a plugin* → Contents) lists every tiddler it contains as a flat alphabetical list. For plugins with many tiddlers organised under path-like titles (`plugin-title/modules/foo`, `plugin-title/language/en-GB/`, …), that flat list gets hard to scan.

This plugin overrides the shadow tiddler `$:/core/ui/PluginInfo/Default/contents` to transclude the core `tree` macro instead, rooted at the inspected plugin's own title. Tiddlers are grouped by `/`-separated path segments, the same way tag trees are rendered elsewhere in TiddlyWiki.

> ⚠️ This is a **global** shadow override: once installed, it changes the Contents tab for every plugin's info panel in the wiki — not just for one specific plugin.

## Installation

**Live demo**: [https://nikorion.github.io/TW-Plugin-Info-Tree/](https://nikorion.github.io/TW-Plugin-Info-Tree/) — try the plugin before installing it.

**From the nikorion plugin library** (TiddlyWiki then offers each new version as an update):

1. In your wiki, create a tiddler tagged `$:/tags/PluginLibrary`, with a field `url` set to `https://nikorion.github.io/tw-dev/library/index.html` and a `caption` such as `nikorion`.
2. Open *Control Panel → Plugins → Get more plugins*, choose the nikorion library and install **Plugin Info Tree**.

**By hand**: download [`TW-Plugin-Info-Tree-Plugin.json`](https://nikorion.github.io/TW-Plugin-Info-Tree/TW-Plugin-Info-Tree-Plugin.json) and drag it onto your wiki.

Requires TiddlyWiki ≥ 5.2.0.

## Development

```
pnpm install
pnpm dev      # dev wiki + hot reload; the URL (random free port) is printed on start
pnpm build    # dist/TW-Plugin-Info-Tree-Plugin.json + docs/ (demo wiki, published by CI)
```

Sources are in `src/plugin-info-tree/`. The dev wiki (`wiki/`) also loads `tiddlywiki/katex` so there's a plugin with enough nested tiddlers to make the tree view worth looking at while developing.

## Files

| File | Role |
|---|---|
| `src/plugin-info-tree/plugin.info` | Plugin metadata |
| `src/plugin-info-tree/plugin-info-override.tid` | Shadow override of `$:/core/ui/PluginInfo/Default/contents` |
| `src/plugin-info-tree/readme.tid` / `licence.tid` / `history.tid` | Plugin info panel tabs |

## Version history

**v0.1.0**

Initial release.

## Credits

Developed with assistance from Anthropic Claude for code, review, and documentation.

## License

MIT License — see `LICENSE`
