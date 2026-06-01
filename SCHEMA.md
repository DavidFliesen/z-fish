# Z-Fish Data Schema (v1)

The “database” for Z-Fish is a set of JSON files in `/data`. Because Z-Fish is a
static GitHub Pages site, there’s no server to run SQLite or Postgres — so the
data lives as version-controlled JSON that the app fetches in the browser. Git
gives you history and diffs, and you can edit the files in GitHub’s web editor.

The whole point of this phase: **data is now separate from logic.** The matching
engine in `index.html` never contains fish or lure facts anymore — it only reads
these files. Add a species or change a lure once, here, and the app updates.

-----

## Files

|File                    |What it holds                             |
|------------------------|------------------------------------------|
|`data/lures.json`       |The Z-Man lure catalog — one row per lure |
|`data/species.json`     |Fish species, each referencing lures by id|
|`data/new-products.json`|The “New From Z-Man” feed                 |

-----

## `lures.json`

```json
{ "id": "jackhammer", "name": "ChatterBait JackHammer",
  "type": "chatter", "colors": "Green Pumpkin · Chartreuse/White", "isNew": false }
```

|Field   |Type  |Notes                                                                      |
|--------|------|---------------------------------------------------------------------------|
|`id`    |string|Stable, unique. Species point at this. Don’t rename casually.              |
|`name`  |string|Display name                                                               |
|`type`  |string|Drives the icon: `chatter`, `soft`, `swim`, `craw`, `shrimp`, `frog`, `jig`|
|`colors`|string|Suggested color pattern(s)                                                 |
|`isNew` |bool  |Shows the NEW badge                                                        |

## `species.json`

```json
{ "id": "lmb", "name": "Largemouth Bass", "scientificName": "Micropterus salmoides",
  "water": "fresh", "icon": "bass",
  "tempMin": 50, "tempMax": 85, "tempOptimal": [62, 78],
  "range": "Widespread across North America; lakes, ponds, slow rivers.",
  "howTo": "Ambush predator on cover …",
  "lures": { "warm": ["jackhammer","popfrogz"], "cold": ["finessetrd"],
             "lowLight": ["popfrogz"], "clear": ["jerkshadz"], "ffs": [] } }
```

|Field                   |Type      |Notes                                                                                                                              |
|------------------------|----------|-----------------------------------------------------------------------------------------------------------------------------------|
|`id`                    |string    |Stable, unique                                                                                                                     |
|`name`, `scientificName`|string    |Display                                                                                                                            |
|`water`                 |string    |`fresh`, `inshore`, or `nearshore` — buckets the species **for now**                                                               |
|`icon`                  |string    |`bass`, `panfish`, `redfish`, `trout`, `flounder`, `catfish`, `toothy`                                                             |
|`tempMin` / `tempMax`   |number    |°F survivable range                                                                                                                |
|`tempOptimal`           |`[lo, hi]`|°F where the fish is most active — drives the score                                                                                |
|`range`                 |string    |Coarse range hint. See “Next phase” below.                                                                                         |
|`howTo`                 |string    |The how-to-catch brief                                                                                                             |
|`lures`                 |object    |Lure **ids** grouped by condition: `warm`, `cold`, `lowLight`, `clear`, `ffs`. The engine picks the top few for current conditions.|


> **Referential integrity:** every id under `lures` must exist in `lures.json`.
> A quick check: each lure id used by a species should match a `lures.json` id.

## `new-products.json`

```json
{ "name": "ChatterBait EVO", "category": "Wire Bait",
  "date": "2026-01-15", "description": "Next-level bladed jig …" }
```

`date` (ISO `YYYY-MM-DD`, optional) lets the app sort newest-first.

-----

## How the app uses it

On load, `index.html`:

1. Fetches the three files.
1. Indexes lures by `id`, groups species by `water`.
1. Geocodes the location, pulls weather, estimates water temp.
1. Scores each species (temp fit + light/wind/pressure) and picks lures by condition.

-----

## Next phase (and why the schema already anticipates it)

The current limitation: species are chosen by the `water` button, **not** by what
actually lives at the location. That’s why a saltwater toggle can show coastal
fish for an inland lake. The fix is to use the location to filter species using
real occurrence data from **iNaturalist / GBIF**, keyed to lat/lon.

The `range` field is the placeholder for that — today it’s a human-readable hint;
later it becomes (or is replaced by) real distribution data so location does the
filtering. Nothing else in the schema needs to change to get there.

This same shape also lifts cleanly into SQLite/Postgres when a backend arrives:
`lures` and `species` become tables, the per-condition lure lists become a join
table (`species_id`, `condition`, `lure_id`), and `range` becomes occurrence rows.