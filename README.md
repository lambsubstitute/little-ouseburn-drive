# Little Ouseburn Drive

Browser driving game set in a 1.5 km × 1.5 km square of real Little Ouseburn, North Yorkshire (British National Grid E 444100–445600, N 460000–461500): the village on Main Street, Holy Trinity church and the Thompson Mausoleum, Moat Hall, the B6265 over Little Ouseburn Bridge, and the farms round Kirby Hall.

Live: https://lambsubstitute.github.io/little-ouseburn-drive/

## Play

```bash
python3 -m http.server 8765 -d game
```

Open http://localhost:8765. WASD/arrows drive, Space handbrake, C camera (chase / bonnet / high / drone), M big map, R back on road, V engine sound (off by default), 1–7 teleport (Village, Church, Mausoleum, Moat Hall, Bridge, Kirby Hall Farm, Broadlands).

## Data

| Source | Used for |
|---|---|
| OpenStreetMap (Overpass) | roads + names, 50 building outlines, water, land use, bus stops, post boxes, benches, walls/fences/hedges |
| Environment Agency LIDAR Composite 1 m DTM, first- and last-return DSM | ground, building heights, roof facets, trees, hedgerows, roofs under trees |
| Historic England NHLE | which buildings are listed (19 entries), grades, names |
| Wikidata + Wikimedia Commons | real wall materials of 6 listed buildings, read by eye from photos (`data/materials_by_photo.txt`) |
| OS OpenMap Local | 119 building outlines where OSM has none (OSM had 50 of the village's ~170) |

Mapillary was queried but has no street-level imagery of this tile, so there are no Mapillary street objects or shopfront colours.

### How it is made, and what is real

- **Buildings**: footprints from OSM, filled in from OS OpenMap Local. Roofs are fitted from the LIDAR surface as separate planar facets (area-weighted median error 0.21 m against the LIDAR; ~10% of roof area is more than 1 m off, mostly edges). 52 buildings sit under trees, so their roofs are read from the last-return LIDAR.
- **Wall materials**: by eye from Commons photos for Holy Trinity (buff stone), Thompson Mausoleum (pale limestone), New Lodge and Old Lodge (grey stone; Old Lodge's photo is distant), Manor Farm House (brick) and Moat Hall (white render). Every other building has a generic material.
- **Village rules** (`"rural": true` in `data/place.json`): no generated street lamps, pavements only where OSM maps them, hedgerows traced from the LIDAR.
- **Trees**: from the LIDAR canopy, generic shapes.

### Known gaps

- One small unnamed building's roof is ~5 m too high, and the Mausoleum's dome is modelled low (domes aren't fitted).
- One hedge line crosses a road near the church.
- Unlisted buildings' wall colours are generic, not observed.

## Rebuild

```bash
python3 -m venv .venv && .venv/bin/pip install -r requirements.txt
.venv/bin/python tools/place.py fetch        # data/place.json is in the repo
.venv/bin/python tools/place.py os           # OS OpenMap Local (~118 MB download)
.venv/bin/python tools/process.py
.venv/bin/python tools/check.py
```

## Publishing

`git subtree push --prefix game origin gh-pages`

Attribution: © OpenStreetMap contributors (ODbL); © Environment Agency (OGL); © Historic England (OGL); contains OS data © Crown copyright and database right 2026 (OGL). Photos used for classification are from Wikimedia Commons under their own licences and are not redistributed here.
