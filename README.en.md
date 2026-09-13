# Quick-Site-Design

*[Deutsche Fassung](README.md)*

**Plan your site layout while the idea is still fresh.**

Quick-Site-Design is a tool for the early stage of construction site layout.
Instead of estimating dimensions or pushing symbols around a site plan, you load
the official terrain model of your site and place what actually goes on it: site
offices, tower cranes, mobile cranes, haul roads. Everything to scale, everything
on real elevations.

The value is in the speed. A crane position is set in two minutes and moved in
two more. You see immediately whether the radius reaches, whether two crane radii
overlap, whether the office block fits the available area, and how the haul road
sits in the terrain. It does not replace detailed design — but it saves the
rounds in which that design would otherwise be discarded three times over.

Technically it is a single HTML file of roughly 140 KB. No server, no
installation, no build step. Open it, drop in a terrain model, get going.

**[→ Open the application](https://Mucnese.github.io/quick-site-design/)**

---

## What it does

**Geospatial data**
- GeoTIFF terrain models, several tiles at once. Pixels from all tiles are
  averaged into a common grid, so differing resolutions and overlaps are not a
  problem.
- CityGML LOD2, also as a batch. Axis order is detected automatically, buildings
  outside the tile are discarded.
- Detail level from 140 grid points up to the untouched source resolution.
- The coordinate reference system is read from the GeoKeys.

**Site elements**
- Container blocks up to 25 side by side, 3 rows, 5 storeys. From the second row
  onwards every other row lies crosswise, like a cross tie.
- 34 tower cranes from Liebherr and WOLFFKRAN, all figures taken from the
  manufacturers' original data sheets.
- 9 Liebherr mobile cranes from 50 t to 750 t.
- Haul roads as a polyline with filleted corners; the alignment drapes onto the
  terrain and nodes can be moved afterwards.

**Interaction**
- Left-click on terrain or buildings sets a measure point and shows easting,
  northing and elevation right at the point.
- Warning when crane radii overlap.
- Three interface styles (AutoCAD, Revit, Forma), eleven typefaces, German and
  English.

---

## Demo

The [`demo/`](demo/) folder holds a complete sample data set, about 8 MB as a
ZIP archive:

| File | Contents |
|---|---|
| `692_5336.tif` | DTM, 1000 × 1000 points, 1 m grid, elevations 503.1 to 520.7 m |
| `692_5336.gml` | LOD2 building model, CityGML 1.0, 2913 buildings |

Both in ETRS89 / UTM zone 32N (EPSG:25832), south-west corner at E 692000 /
N 5336000, located at roughly 48.153 North and 11.588 East.

**Getting started:**

1. Download the ZIP and unpack it
2. Open `index.html` in a browser
3. Under **Data → Terrain · GeoTIFF** pick `692_5336.tif`
4. Then under **Buildings · CityGML** pick `692_5336.gml`
5. Choose an element from the bar at the bottom and click on the terrain

Two things are normal here. Reading the building file takes a few seconds
depending on your machine, because the uncompressed XML is 74 MB. And the
building file covers roughly 2 × 2 km, more than the terrain tile — everything
outside is discarded on load, leaving around 16,000 surfaces. The status line
reports both figures.

The data originates from official German surveying data. When reusing it,
observe the terms of the issuing state authority.

---

## Important note on the crane data

The crane figures are **guide values**. They come from the manufacturers'
published data sheets, but they depend on the rigging configuration, tower
combination and ballasting.

**For actual planning, only the load chart of the specific crane is
authoritative.** This application does not replace structural verification,
stability calculations, or coordination with the crane hire company.

Outrigger dimensions and carrier lengths serve presentation only and are
schematic.

---

## Where to get the data

In Germany, terrain and building models are available free of charge from the
state surveying authorities. The portals differ by federal state; search for
"DGM1" or "LoD2" together with the state name. Most states publish the data
under the Datenlizenz Deutschland.

The application expects GeoTIFF with elevation values as float and a
georeference in the header. For buildings, the usual LOD2 output of the German
states works as is. Data from other countries works too, as long as the GeoTIFF
carries a projected coordinate system and the CityGML uses the same one.

---

## Running it yourself

`index.html` is self-contained. Three libraries are loaded from a CDN at runtime:

| Library | Licence |
|---|---|
| three.js r128 | MIT |
| OrbitControls (three.js examples) | MIT |
| geotiff.js 2.1.3 | MIT |

All three are compatible with the GPLv3. Without an internet connection the
application will not start; for offline use the libraries have to be shipped
alongside and referenced locally.

### With GitHub Pages

1. Create a repository named `quick-site-design`, visibility **Public**
2. Upload the contents of this folder
3. Settings → Pages → Source: `Deploy from a branch`, branch `main`, folder
   `/ (root)`
4. After a minute or two the page is live at
   `https://Mucnese.github.io/quick-site-design/`

If you name the repository differently, the last part of the address changes
accordingly — remember to update the link at the top of this file.

### On your own web space

Put `index.html` in the web directory. That is all.

---

## Development

The delivered file is assembled from four source files:

```
src/shell_head.html   HTML scaffold and CSS
src/app.js            scene, terrain, site elements, geometry
src/ui.js             user interface
src/boot.js           start-up sequence and render loop
```

Build with `python3 build.py`. Tests run against purpose-built stubs, so no
browser is needed:

```bash
node test/test.js            # core logic
node test/test-ui.js         # interface in the shared scope
node test/validate-html.js   # the assembled file
```

All three must pass before shipping. The conventions that the code relies on are
documented in `PROJEKT.md` (German).

---

## Licence

GNU General Public License, version 3 or later. See [LICENSE](LICENSE).

In short: you may use, distribute and modify the software. If you distribute a
modified version, that version must also be under the GPLv3 and its source must
be available.

The software is provided without any warranty.
