# BetterPolyAi

A standalone client-side mod extracted from **MindustryX**, focused on Poly build assist.

## Features
- Adds a toggleable Poly build-assist mode with a keybind.
- Only reacts to your own build plans.
- Does not rebuild or assist plans created by other players.
- Mutually exclusive with MindustryX BuilderAI: yields unit control when X has BuilderAI selected (can be disabled in settings).
- In-game settings (Settings → Better PolyAI): enable toggle, yield to MindustryX, update check, etc.

## Install
- Download one of these artifacts from Releases:
  - `betterPolyAi-<version>.zip`
  - `betterPolyAi-<version>.jar`
  - `betterPolyAi-<version>-android.jar`
- Put it into your Mindustry `mods` directory and restart the game.

## Build
```bash
gradle deploy
```
