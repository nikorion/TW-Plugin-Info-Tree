# TW-Plugin-Info-Tree

[English](README.md) · **Français**

![Status](https://img.shields.io/badge/status-experimental-orange)
![TiddlyWiki](https://img.shields.io/badge/TiddlyWiki-%E2%89%A55.2.0-blue)

Un plugin TiddlyWiki qui remplace la liste à plat des tiddlers de l'onglet **Contents** du panneau d'information d'un plugin par une arborescence.

## Présentation

Par défaut, l'onglet « Contents » du panneau d'information d'un plugin (panneau de contrôle → Plugins → *choisir un plugin* → Contents) liste tous les tiddlers qu'il contient sous forme de liste alphabétique à plat. Pour les plugins comptant de nombreux tiddlers rangés sous des titres en forme de chemin (`plugin-title/modules/foo`, `plugin-title/language/en-GB/`, …), cette liste devient difficile à parcourir.

Ce plugin surcharge le shadow tiddler `$:/core/ui/PluginInfo/Default/contents` pour transclure à la place la macro `tree` du core, avec pour racine le titre du plugin inspecté. Les tiddlers sont regroupés par segments de chemin séparés par des `/`, comme les arbres de tags affichés ailleurs dans TiddlyWiki.

> ⚠️ C'est une surcharge **globale** d'un shadow : une fois installé, il modifie l'onglet Contents du panneau d'information de tous les plugins du wiki — pas seulement d'un plugin en particulier.

## Installation

**Démo en ligne** : [https://nikorion.github.io/TW-Plugin-Info-Tree/](https://nikorion.github.io/TW-Plugin-Info-Tree/) — pour essayer le plugin avant de l'installer.

**Depuis la bibliothèque de plugins nikorion** (TiddlyWiki propose ensuite chaque nouvelle version en mise à jour) :

1. Dans votre wiki, créer un tiddler tagué `$:/tags/PluginLibrary`, avec un champ `url` valant `https://nikorion.github.io/tw-dev/library/index.html` et une `caption` comme `nikorion`.
2. Ouvrir *Panneau de configuration → Plugins → Obtenir d'autres plugins*, choisir la bibliothèque nikorion et installer **Plugin Info Tree**.

**À la main** : télécharger [`TW-Plugin-Info-Tree-Plugin.json`](https://nikorion.github.io/TW-Plugin-Info-Tree/TW-Plugin-Info-Tree-Plugin.json) et le glisser-déposer sur votre wiki.

Nécessite TiddlyWiki ≥ 5.2.0.

## Développement

```
pnpm install
pnpm dev      # wiki de dev + rechargement à chaud ; l'URL (port libre aléatoire) s'affiche au démarrage
pnpm build    # dist/TW-Plugin-Info-Tree-Plugin.json + docs/ (wiki de démo, publié par la CI)
```

Les sources sont dans `src/plugin-info-tree/`. Le wiki de dev (`wiki/`) charge aussi `tiddlywiki/katex`, pour disposer pendant le développement d'un plugin aux tiddlers suffisamment imbriqués pour que l'arborescence vaille le coup d'œil.

## Fichiers

| Fichier | Rôle |
|---|---|
| `src/plugin-info-tree/plugin.info` | Métadonnées du plugin |
| `src/plugin-info-tree/plugin-info-override.tid` | Surcharge du shadow `$:/core/ui/PluginInfo/Default/contents` |
| `src/plugin-info-tree/readme.tid` / `licence.tid` / `history.tid` | Onglets du panneau d'information du plugin |

## Historique des versions

**v0.1.0**

Première version.

## Crédits

Développé avec l'aide d'Anthropic Claude pour le code, la revue et la documentation.

## Licence

Licence MIT — voir `LICENSE`
