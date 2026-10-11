# TW-Plugin-Info-Tree — contexte projet pour Claude

> **Avant toute tâche sur ce plugin, consulter d'abord le `CLAUDE.md` du workspace** (`../CLAUDE.md`) et ses `guides/` : outillage de dev commun (pnpm, `dev.cjs`/HMR, Ctrl+C, git push), pièges PowerShell/Windows, `publishFilter`, symlink. Ci-dessous : uniquement le spécifique à TW-Plugin-Info-Tree.

## Ce que c'est
Plugin TiddlyWiki (`$:/plugins/nikorion/plugin-info-tree`) qui remplace l'onglet "Contents" du panneau d'info des plugins par une vue arborescente au lieu de la liste plate par défaut. Auteur : nikorion. **Pas de JS de plugin, pas d'ESLint** : que des tiddlers wikitext/JSON.

## Structure
```
src/plugin-info-tree/            ← sources du plugin (seul dossier à toucher)
  plugin-info-override.tid       ← override shadow de $:/core/ui/PluginInfo/Default/contents
  macros/tree.tid                 ← macros nk-tree / nk-tree-node / nk-branch-node / nk-leaf-node
  readme.tid
  licence.tid
  history.tid
  plugin.info                    ← métadonnées du plugin (v0.1.0)

wiki/                            ← wiki TW de développement
  tiddlywiki.info                ← plugins actifs (dont tiddlywiki/katex, pour avoir un plugin "riche" à inspecter)
  tiddlers/
    system/$__config_SyncFilter.tid
    $__DefaultTiddlers.tid
    $__SiteTitle.tid / $__SiteSubtitle.tid

dist/                            ← généré par pnpm build, gitignored
docs/                             ← démo générée par `pnpm build` (`index.html` + moteur externe), gitignorée, publiée par la CI
```

## Ce que fait le plugin
`plugin-info-override.tid` surcharge le tiddler shadow `$:/core/ui/PluginInfo/Default/contents` et affiche, sous un titre de premier niveau `!` (`Tree/Heading`, « Shadows embarqués dans le plugin », 2026-10-12), jusqu'à deux arbres, chacun précédé d'un titre wikitext `!!` et affiché seulement s'il n'est pas vide :

1. **Tiddlers du namespace** (`Tree/PluginShadows`) — tiddlers sous `<plugin-title>/`, avec la ligne du préfixe en tête (`show-prefix`)
2. **Autres** (`Tree/Overrides`) — tout le reste de `plugintiddlers[]` (surcharges du cœur ou de plugins tiers, tiddlers globaux ; fusion des anciens groupes « Core overrides » et « Other shadow tiddlers », 2026-10-11)

Les deux listes sont calculées dans des `$let` avec `[<currentTiddler>plugintiddlers[]prefix<prefixText>]` / `!prefix<prefixText>` puis **`+[format:titlelist[]join[ ]]`** : **piège vécu 2026-10-11** — `{{{ filtre }}}` en attribut de `$let` ne garde que le premier résultat, donc sans ce sérialiseur l'arbre ne recevait qu'un titre.

Rendu par la macro locale `nk-tree` (`macros/tree.tid`) — copie de la logique récursive branche/feuille de la macro core `tree`, généralisée à une liste de titres via `enlist<source>`. **Repli des dossiers à enfant unique** (2026-10-11) : `nk-branch-node` compte les fichiers et sous-dossiers directs ; s'il n'y a aucun fichier et exactement un sous-dossier, il se rappelle avec le chunk allongé (`themes/` + `tiddlywiki/` → `themes/tiddlywiki/`) — l'état de dépliage et le compteur utilisent le chunk final.

Groupes testables hors navigateur : wiki temporaire (voir le `CLAUDE.md` du workspace), en important aussi `$:/tags/Global` (`plugin-info-tree-lingo-text` est une fonction globale, sinon les titres sont vides).

⚠️ **Effet de bord global** : comme c'est un override de shadow tiddler core, il s'applique à ''tous'' les plugins inspectés dans le wiki, pas seulement à ce plugin. À rappeler dans la doc pour quiconque l'installe.

## Spécificités dev
- `pnpm build` → `dist/TW-Plugin-Info-Tree-Plugin.json` + démo `docs/` (publiée par la CI : `../guides/publication.md`). Démo `publishFilter` (`../guides/build-html-publishfilter.md`) : `katex` gardé intentionnellement (plugin "riche" démontrant la vue arborescente).
- HMR : les `.tid` (dont `macros/tree.tid` et l'override) sont poussés à chaud ; seul `plugin.info` reboote.
- Pour tester visuellement le rendu : panneau de contrôle → Plugins → un plugin avec plusieurs tiddlers (ex. KaTeX, chargé dans le wiki de dev) → onglet Contents.
