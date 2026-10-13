# Adding or updating a mod

## 1. Prepare your mod folder

```
my-mod/
├── mod.toml
└── Playtime_Multiplayer/
    └── Content/
        └── ...          your changed files, at the same paths as in the game
```

`mod.toml`:

```toml
id          = "my-mod"                 # lowercase letters, digits, dashes
name        = "My Mod"
version     = "1.0.0"                  # semver, raise it for every update
authors     = ["your name"]
summary     = "One sentence about what it does."
description = """
Longer text shown on the mod page."""
category    = "fixes"                  # multiplayer, interface, fixes, offline, other
network     = "safe"                   # see below
game_builds = ["12576441"]             # Steam build ids, see registry.toml
color       = "#3A6BFF"                # optional card color
requires    = []                       # optional: ids of library mods this mod needs

[[changelog]]
version = "1.0.0"
changes = ["First release"]
```

`network` tells players what your mod means for multiplayer:

| value | meaning |
| --- | --- |
| `safe` | does not change anything that is sent over the network. Players can still join public matches with people who have no mods. |
| `lobby` | changes networked behaviour. Everyone in a private lobby needs the mod. |
| `offline` | only used in tutorials or solo play. |

Adding RPC functions or replicated variables to replicated blueprints breaks matchmaking with unmodded players. Such a mod must be `lobby`.

## 2. Pack it

**In the app:** open **Submit a mod**, choose your folder, then press **Send as issue** and attach the zip.

**With the command line:** clone this repository, then run

```sh
cargo install --git https://github.com/MCbabel/playkit playkit-pack
playkit-pack pack path/to/my-mod --repo .
playkit-pack index . --base-url https://raw.githubusercontent.com/MCbabel/playkit-mods/main
```

This writes `mods/my-mod/` with `mod.toml`, `files.json` and the patches. Commit that folder (not `index.json`) and open a pull request.

## 3. Review

The `Validate` check runs on every pull request. A maintainer looks at what the mod changes and confirms the `network` value before merging. After the merge, `index.json` is rebuilt and the mod shows up in Playkit.

## Shared parts (library mods)

If several mods need the same groundwork, put it in its own mod with `category = "library"` and list its id in `requires` of every mod that needs it. Playkit installs libraries automatically, hides them from the mod list, and removes them again when no installed mod needs them. Only use `requires` for real technical needs, never to bundle unrelated features.

## Mods that change the same file

You do not need to coordinate with other authors. If two mods change the same asset, Playkit combines them on the player's machine. This works best when your mod extends the original instead of rewriting it: add new variables, functions and events, and hook into existing code instead of replacing whole graphs. If two mods edit the same instruction or property differently, Playkit refuses to install the second one and tells the player.

## Rules

- Only files below `Playtime_Multiplayer/Content`. No programs, scripts or DLLs.
- No cheats that give an advantage in public matches.
- No content you are not allowed to share.
