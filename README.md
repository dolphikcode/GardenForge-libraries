# GardenForge-libraries

Object library for [GardenForge](../GardenForge-kotlin) — a garden-planning
Android app. The app downloads `library/index.json` at runtime via
`raw.githubusercontent.com` and lazily fetches the referenced assets (icons,
previews, 3D models), caching them on-device.

## Layout

```
library/
  index.json                   — catalog of all library items
  icons/<category>/<id>.png    — top-down icon drawn on the plan (≤512 px)
  previews/<id>.png            — optional larger image for the picker sheet
  models/<id>.glb              — optional 3D model (glTF 2.0 binary)
```

## index.json schema

```json
{
  "schemaVersion": 1,
  "items": [ { ...item... } ]
}
```

Item fields:

| Field | Required | Meaning |
|---|---|---|
| `id` | yes | unique stable id — persisted on placed elements |
| `category` | yes | layer category the item belongs to: `water`, `electricity`, `garden_bed`, `vegetables`, `grass`, `herbs`, `shrubs`, `trees`, `custom` |
| `type` | yes | element kind persisted on the element (`raised_bed`, `pipe`, `well`, `herb`, `tree`, …) — free string |
| `geometry` | yes | `rect` (sized footprint stamp), `point` (icon stamp), `polyline` / `polygon` (free draw) |
| `name` | yes | localized names — `{ "en": "...", "pl": "..." }` |
| `description` | no | localized descriptions, same shape as `name` |
| `widthM`, `lengthM` | for `rect` | real footprint dimensions in meters |
| `icon` | no | path to top-down PNG (transparent background, ≤512 px) |
| `preview` | no | path to a larger picker image |
| `model` | no | path to a `.glb` (glTF 2.0 binary) 3D model — future use |
| `attributes` | no | free-form key/value map copied onto placed elements |
| `care` | no | plants only — open object (`light`, `watering`, `soil`, `pruning`, …), merged into element attributes as `care.<key>` |

## 3D models

Use **glTF 2.0 binary (`.glb`)** — a single file containing geometry,
materials and textures. Blender exports it natively. The app renders `.glb`
via Google Filament (SceneView). `.gltf` + separate textures also works but
`.glb` is preferred (one download).

## Adding an item

1. Add the entry to `library/index.json` (bump nothing else — `schemaVersion`
   only changes on format changes).
2. Commit its icon (and preview/model if any) under the right folders.
3. Push — the app picks it up on the next library refresh.
