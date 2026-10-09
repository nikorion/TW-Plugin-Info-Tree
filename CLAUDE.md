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
`plugin-info-override.tid` surcharge le tiddler shadow `$:/core/ui/PluginInfo/Default/contents` et affiche jusqu'à trois arbres, chacun affiché seulement s'il n'est pas vide :

1. **Plugin tiddlers** — via la macro core `tree`, préfixée par `<plugin-title>/` (inchangé depuis la v0.1.0)
2. **Core overrides** — tiddlers du plugin (hors groupe 1) dont le titre correspond aussi à un tiddler de `$:/core`
3. **Other shadow tiddlers** — tout le reste (ni dans le groupe 1, ni dans le groupe 2)

Les groupes 2 et 3 sont calculés à partir de `[<currentTiddler>plugintiddlers[]]` (liste complète des tiddlers embarqués dans le plugin, peu importe leur nommage), puis filtrés avec des runs `-` (except) et `:intersection` contre `[[$:/core]plugintiddlers[]]` :

```
core-overrides = [<currentTiddler>plugintiddlers[]] -[prefix<prefixText>] :intersection[[$:/core]plugintiddlers[]]
other-shadows  = [<currentTiddler>plugintiddlers[]] -[prefix<prefixText>] -[[$:/core]plugintiddlers[]]
```

Ils sont rendus par une macro locale `nk-tree` (`macros/tree.tid`) — une copie de la logique récursive branch/leaf de la macro core `tree`, généralisée pour parcourir une liste de titres arbitraire via `enlist<source>` au lieu du filtre figé `all[shadows+tiddlers]`. Le groupe 1 continue d'utiliser directement la macro core `tree`, qui gère déjà le filtrage par préfixe nativement.

⚠️ **Effet de bord global** : comme c'est un override de shadow tiddler core, il s'applique à ''tous'' les plugins inspectés dans le wiki, pas seulement à ce plugin. À rappeler dans la doc pour quiconque l'installe.

## Spécificités dev
- `pnpm build` → `dist/TW-Plugin-Info-Tree-Plugin.json` + démo `docs/` (publiée par la CI : `../guides/publication.md`). Démo `publishFilter` (`../guides/build-html-publishfilter.md`) : `katex` gardé intentionnellement (plugin "riche" démontrant la vue arborescente).
- HMR : les `.tid` (dont `macros/tree.tid` et l'override) sont poussés à chaud ; seul `plugin.info` reboote.
- Pour tester visuellement le rendu : panneau de contrôle → Plugins → un plugin avec plusieurs tiddlers (ex. KaTeX, chargé dans le wiki de dev) → onglet Contents.
