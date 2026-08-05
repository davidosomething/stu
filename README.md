# STU (Stone Technology Ultimate)

Minecraft server running the [Stone Technology Ultimate](https://www.curseforge.com/minecraft/modpacks/stone-technology-ultimate) modpack via [docker-minecraft-server](https://github.com/itzg/docker-minecraft-server).

## Source

The server pack (`stone-technology-ultimate-server.zip`) was obtained from the modpack's [official Discord](https://discord.gg/stone-technology). Unlike the CurseForge client download, the server pack includes all 236 mod JARs pre-bundled — no CurseForge API or force-include workarounds needed.

## Prerequisites

- Podman v5+ (comes preinstalled on Aurora-DX)
- [Podman Desktop](https://podman-desktop.io/) running (or `systemctl --user start podman.socket`) to provide the podman API socket
- At least 6 GB of RAM available for the container

## First-time setup

```bash
cd stu
mkdir -p data
unzip extras/stone-technology-ultimate-server.zip -d data
podman compose up -d
```

## Running

```bash
podman compose up -d
```

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

All world data, configs, and mods live in `./data/` and persist across container restarts.

`LEVEL_TYPE: "skyblockbuilder:skyblock"` in `compose.yaml` ensures the stoneblock world generator is used. The server pack's own `server.properties` also has this set.

## Updating the modpack

1. Download the new server pack ZIP from the modpack's Discord
2. `podman compose down`
3. Back up `./data/world*`, `./data/server.properties`, and `./data/ops.json`
4. `sudo rm -rf ./data`
5. Extract the new server pack: `unzip <new-server-pack>.zip -d data`
6. Restore your world and config backups into `./data/`
7. `podman compose up -d`

For a fresh start (no world preservation), skip steps 3 and 6.
