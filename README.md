# Standby extension kit

Tools for [Standby](https://github.com/RagedUnicorn/standby) element extensions: the targets, steel, movers, activators, props and boards Standby draws on a stage plan, in its PDF briefings and in its 3D walkthrough. Each extension lives in a repository of its own, where its drawings and models are added, fixed and changed. A release publishes a package that Standby installs (Settings, Extensions).

The kit has three parts:

- **`standby-extension`**, a command line: checks an extension as its CI does, packs a release, builds the viewer site, serves the viewer on a folder while you work, and starts a new element.
- **The viewer**: every element of an extension, drawn by Standby's own renderers in 2D (as on the plan, on a 0.1 m grid) and 3D (beside a 1.8 m figure, an arrow showing the way it faces).
- **The workflow** an extension repository calls: checks, a viewer preview of each pull request with its changes before and after, and on a tag, a release and the viewer on GitHub Pages.

## An extension

```
extension.json                     the manifest
elements/<category>/<id>.json      an element (targets, steel, movers, activators, props)
boards/<id>.json                   a board: a target's outline, hard-cover lines and no-shoot mark
assets/2d/<id>.svg                 an element's drawing; assets/2d/<id>--<view>.svg its other views
assets/3d/<id>.glb                 an element's model
README.md, LICENSE
```

Each file names its schema in `$schema`, so an editor checks and completes it: [extension](schema/v1/extension.json), [element](schema/v1/element.json), [board](schema/v1/board.json).

- **Ids.** An element's id is its file name and never changes once stages use it. A new extension's ids start with its own id (`club.car-door` in extension `club`). The ids of Standby's default extensions are taken.
- **Drawn by Standby or from files.** `art.kind` is one of Standby's builders (`board`, `trap`, `popper`, `plate`, `swinger`, `updown`, `turner`, `flipper`), which move, carry other targets or take hits and draw from the element's file and the boards. Or it is `asset`: drawn from its files.
- **Drawings** (`assets/2d/*.svg`) are front views in mm at miniature scale 1: the plan draws them times the stage's miniature scale. The foot point is the bottom centre of the viewBox, or `art.foot`. Only shapes, groups, transforms, fills, strokes, opacity, gradients, clip paths and masks pointing inside the file are kept. Text, images, scripts, styles, links, `<use>` and anything pointing outside the file are refused. Colours are literal.
- **Models** (`assets/3d/*.glb`) are glTF 2.0 binaries in metres, Y up, the foot point at the origin, facing +Z (the shooter), everything inside the file, 4 MB at most, no Draco or meshopt. A push button's or a pedal's moving part is the node named `press`. Their colours are used as they are: pick them to suit the walkthrough's soft look.
- **Behaviour** is data: `blocks` (hides what is behind it), `footprint` (walked round), `surfaces` (where something can be put down), `activation` (`step`, `pull`, `press`), `mount` (where a target's face starts), `onSurface` (put down on a prop, it lies on its top).
- **A discipline's extension** gives its boards and `stageDefaults`: the board movers and app-made targets take on its stages, and the target a bullet trap carries.

## The command line

Node 24 or newer. From a release of this repository:

```
npx https://github.com/RagedUnicorn/standby-extension-kit/releases/download/v0.1.0/standby-extension-kit-0.1.0.tgz view
```

| Command | What it does |
|---|---|
| `standby-extension validate [folder]` | Checks every file, as the CI does |
| `standby-extension pack [folder] [--out dist]` | Writes `<id>-<version>.zip` and prints its sha256 |
| `standby-extension site [folder] [--out _site] [--base folder]` | Builds the viewer site; with `--base`, the compare view of what changed |
| `standby-extension view [folder] [--port 4310]` | The viewer on a folder, reloading as you save |
| `standby-extension new <category> <id> [folder]` | A new element from a template of its category |

To see your work in Standby itself, link the folder: Settings, Extensions, Link a folder. It loads instead of any installed version and reloads as you save.

## A repository

1. Make a repository for the extension (public, so Standby installs from it without a token) with its files at the root, a licence and a readme.
2. Add `.github/workflows/extension.yml` from [templates/extension.yml](templates/extension.yml): it calls this repository's workflow.
3. To release, set `version` in extension.json, merge, and push the tag `v<version>`. The workflow releases `<id>-<version>.zip` and publishes the viewer to the `gh-pages` branch, which the first release (or pull request preview) makes.
4. Once that branch exists, turn on GitHub Pages from it (Settings, Pages, deploy from the `gh-pages` branch, root): the viewer and the pull request previews are served from there.

Standby offers the release as an update to everyone who has the extension. It never installs an update by itself.

## Licence

MIT, as Standby.
