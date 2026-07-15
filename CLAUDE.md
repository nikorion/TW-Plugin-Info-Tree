# TW-Plugin-Info-Tree — contexte projet pour Claude

## Ce que c'est
Plugin TiddlyWiki (`$:/plugins/nikorion/plugin-info-tree`) qui remplace l'onglet "Contents" du panneau d'info des plugins par une vue arborescente au lieu de la liste plate par défaut. Auteur : nikorion.

## Structure
```
src/plugin-info-tree/            ← sources du plugin (seul dossier à toucher)
  plugin-info-override.tid       ← override shadow de $:/core/ui/PluginInfo/Default/contents
  macros/tree.tid                 ← macros pit-tree / pit-tree-node / pit-branch-node / pit-leaf-node
  readme.tid
  licence.tid
  history.tid
  plugin.info                    ← métadonnées du plugin (v0.1.0)

wiki/                            ← wiki TW de développement
  tiddlywiki.info                ← plugins actifs (dont tiddlywiki/katex, pour avoir un plugin "riche" à inspecter), pluginPath: ../src
  tiddlers/
    system/$__dev-hmr.tid
    system/$__config_SyncFilter.tid
    $__DefaultTiddlers.tid
    $__SiteTitle.tid / $__SiteSubtitle.tid

dist/                            ← généré par pnpm build, gitignored
docs/                             ← TW-Plugin-Info-Tree-Wiki.html généré par pnpm build
scripts/
  dev.cjs                         ← orchestrateur pnpm dev (résout les ports, spawn nodemon + dev-hmr)
  dev-hmr.cjs                     ← serveur SSE de HMR de contenu (reboot/reload sur changement de plugin.info)
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

Ils sont rendus par une macro locale `pit-tree` (`macros/tree.tid`) — une copie de la logique récursive branch/leaf de la macro core `tree`, généralisée pour parcourir une liste de titres arbitraire via `enlist<source>` au lieu du filtre figé `all[shadows+tiddlers]`. Le groupe 1 continue d'utiliser directement la macro core `tree`, qui gère déjà le filtrage par préfixe nativement.

⚠️ **Effet de bord global** : comme c'est un override de shadow tiddler core, il s'applique à ''tous'' les plugins inspectés dans le wiki, pas seulement à ce plugin. À rappeler dans la doc pour quiconque l'installe.

## Build html (docs/) — publishFilter
`--rendertiddler $:/core/save/all` embarque tout le store de tiddlers chargé dans wiki, pas seulement le plugin. La target `html` passe la variable `publishFilter` (mécanisme core du bouton "Download full wiki") en args supplémentaires de `--rendertiddler` pour exclure les tiddlers de dev :
```json
"--rendertiddler", "$:/core/save/all", "TW-Plugin-Info-Tree-Wiki.html", "text/plain", "",
"publishFilter", "-[[$:/dev/hmr]] -[[$:/config/dev/hmr-port]] -[[$:/config/SyncFilter]] -[[$:/plugins/wikilabs/link-to-tabs]] -[[$:/plugins/kookma/commander]] -[[$:/plugins/oeyoews/tiddlywiki-codemirror-6]]"
```
Le `""` avant `publishFilter` est le slot `template` (inutilisé, à laisser vide sinon les index se décalent). Les tiddlers de dev du HMR sont exclus ; `link-to-tabs`, `commander` et `codemirror-6` sont installés dans `wiki/tiddlers/` (glisser-déposé, confort de dev) mais exclus du build — `katex` est gardé intentionnellement, c'est le plugin "riche" utilisé pour démontrer la vue arborescente.

## Workflow dev
```
pnpm install
pnpm dev      # TW sur :8080 (défaut ; port libre si occupé) + HMR SSE — l'URL est affichée
pnpm build    # génère dist/TW-Plugin-Info-Tree-Plugin.json + docs/TW-Plugin-Info-Tree-Wiki.html
```

`pnpm dev` lance `scripts/dev.cjs`, orchestrateur (calqué sur TW-Hover-Tilt, zéro dépendance ajoutée) : il résout le port TW (défaut **8080**, sinon un port libre si occupé) et le port SSE du HMR (défaut **35730**, même logique) — « move aside » — écrit le port SSE dans le tiddler git-ignoré `$:/config/dev/hmr-port` (lu par le client navigateur), puis lance en parallèle (spawn direct, plus de `concurrently`) **nodemon** (reboote TW sur changement de `plugin.info` seulement) et **`dev-hmr.cjs`** (serveur SSE de HMR de contenu : les `.tid`, dont `macros/tree.tid` et l'override, sont poussés à chaud, état préservé). Le garde-fou `$:/config/SyncFilter` (`wiki/tiddlers/system/$__config_SyncFilter.tid`) tient le préfixe du plugin hors du sync tiddlyweb pour que les overrides HMR ne soient pas persistés. Principe détaillé : `../guides/hmr-tiddlywiki.md`.

Pas de JS de plugin, pas d'ESLint : ce plugin ne contient que des tiddlers wikitext/JSON. Pour tester visuellement le rendu, ouvrir le panneau de contrôle → Plugins → un plugin avec plusieurs tiddlers (ex. KaTeX, chargé dans le wiki de dev) → onglet Contents.

## Fichiers de config
- [package.json](package.json) — scripts pnpm, dépendances dev
- [scripts/dev.cjs](scripts/dev.cjs) — orchestrateur `pnpm dev` : résolution des ports (défaut/libre) + spawn nodemon & dev-hmr
- [scripts/dev-hmr.cjs](scripts/dev-hmr.cjs) — serveur SSE de HMR de contenu (+ reboot/reload sur changement de `plugin.info`)
- [nodemon.json](nodemon.json) — watch `src/plugin-info-tree/plugin.info` seulement ; port de fallback standalone (`dev.cjs` surcharge le port réel)
- [wiki/tiddlywiki.info](wiki/tiddlywiki.info) — plugins actifs, pluginPath: `../src`

## Symlink TIDDLYWIKI_PLUGIN_PATH
```
C:\Users\Nico\tw\plugins\nikorion\plugin-info-tree → D:\projets\devops\tw\plugins\nikorion\TW-Plugin-Info-Tree\src\plugin-info-tree
```

## Points d'attention Windows
- `pnpm dev` nécessite **deux Ctrl+C** pour quitter (comportement normal sur Windows).
- Voir le CLAUDE.md de TW-Math (`D:\projets\devops\tw\plugins\nikorion\TW-Math\CLAUDE.md`) pour les pièges PowerShell : BOM UTF-8, fins de ligne CRLF, caractères Unicode, etc.
