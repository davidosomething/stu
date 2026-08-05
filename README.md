# STU (Stone Technology Ultimate)

Minecraft server running the [Stone Technology Ultimate](https://www.curseforge.com/minecraft/modpacks/stone-technology-ultimate) modpack via [docker-minecraft-server](https://github.com/itzg/docker-minecraft-server).

## Prerequisites

- Podman v5+ (comes preinstalled on Aurora-DX)
- [Podman Desktop](https://podman-desktop.io/) running (or `systemctl --user start podman.socket`) to provide the podman API socket
- At least 6 GB of RAM available for the container

## Setup

```bash
cd stu
```

## Running

```bash
podman compose up -d
```

On first launch, the container will download the modpack from CurseForge, install the Forge mod loader, and set up the world. This takes several minutes depending on your connection.

## Stopping

```bash
podman compose down
```

## Connecting

The server binds to port **25566** (external). Connect in Minecraft with:

```
localhost:25566
```



## Data

- `LEVEL_TYPE: "skyblockbuilder:skyblock"` — switches the world generator to the stoneblock world. The modpack's singleplayer defaults use Skyblock Builder; without this the server generates a normal world. During updates, verify this is still set and delete the old world (`./data/world*`) so it regenerates correctly.

All world data, configs, and mods live in `./data/` and persist across container restarts.

## Modpack source

The modpack ZIP is stored locally in `./extras/stone-technology-ultimate.zip`. This is used as the primary source via `CF_MODPACK_ZIP`, which ensures all mods are available — including client-only ones that CurseForge's API won't serve for automated download.

`CF_PAGE_URL` is still set so the container can detect the mod loader and Minecraft version from the modpack metadata.

### Client-only mods workaround

Some mods the pack depends on are tagged as client-only on CurseForge, so the container skips them even when installing from the local ZIP. `CF_FORCE_INCLUDE_MODS` in `compose.yaml` lists their CurseForge project IDs to force their inclusion:

| Mod | CurseForge ID | Backup source |
|-----|--------------|---------------|
| Iceberg | `520110` | `./extras/Iceberg-1.18.2-forge-1.0.49.jar` (Modrinth) |
| Advancement Plaques | `499826` | `./extras/AdvancementPlaques-1.18.2-1.4.5.1.jar` (Modrinth) |

If `CF_FORCE_INCLUDE_MODS` stops working on a future update, manually drop the backup jars into `./data/mods/` after the container finishes its first startup and restart.

## Updating the modpack

1. Download the new modpack ZIP from [CurseForge](https://www.curseforge.com/minecraft/modpacks/stone-technology-ultimate)
2. `podman compose down`
3. Replace `./extras/stone-technology-ultimate.zip` with the new version
4. Re-download updated versions of Iceberg and Advancement Plaques from Modrinth to `./extras/` (see table above)
5. Check if new mods are missing — if so, add their CurseForge IDs to `CF_FORCE_INCLUDE_MODS` in `compose.yaml`
6. Pin the version: set `CF_FILENAME_MATCHER` in `compose.yaml` to the new version string
7. `podman compose up -d`

World data in `./data/` is preserved across updates. If you want a fresh start, `sudo rm -rf ./data` before step 7.
