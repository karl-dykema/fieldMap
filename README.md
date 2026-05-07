# Newaygo Field Map

**[Live Map → karl-dykema.github.io/fieldMap](https://karl-dykema.github.io/fieldMap/)**

An interactive web map for exploring land parcels, ecological layers, conservation data, and barrens species observations in Newaygo County, Michigan. Single HTML file, no build step — runs anywhere with a browser.

---

## Features

### Base Layers
- **OpenStreetMap** — road network and place names
- **ESRI Satellite** — high-resolution aerial imagery
- **ESRI Topo** — topographic contour reference

### Parcel Layer
- Loads county parcel records on demand, tiled by viewport
- Click any parcel for owner, acreage, address, PIN, class, SEV, taxable value
- **Fuzzy ownership grouping** (Jaro-Winkler similarity) — parcels with the same beneficial owner are dissolved into a single outline, with combined acreage and assessed values in the popup
- 3,000-feature tile cache; re-requests only uncached cells

### Public Lands
One-toggle overlay showing seven ownership categories in distinct colors:

| Category | Color | Source |
|---|---|---|
| School Forests | Purple | County parcel records |
| USFS (Manistee NF) | Dark green | County parcel records |
| MI DNR | Teal | [DNR LOTS Parcels](https://services3.arcgis.com/Jdnp1TjADvSDxMAX/arcgis/rest/services/DNRLOTSParcelsOPENDATA/FeatureServer/0) |
| County / City | Brown | County parcel records |
| Township | Blue-teal | County parcel records |
| Cemetery | Mauve | County parcel records |
| Conservancy / Preserve | Green | County parcel records |

### Browse Panel
Flip through public land parcels one by one — choose a category, then page forward/back with prev/next arrows. Clicking a result flies to it and opens its popup.

### Ecological & Land Cover Layers
- **Pre-settlement Vegetation** — Michigan circa 1800 cover types (MNFI)
- **Land Cover (NLCD 2021)** — current 30m classified raster (MRLC)
- **Soils** — USDA Web Soil Survey MapunitPoly (NRCS WMS)
- **Wetlands (Part 303)** — Michigan EGLE regulated wetlands inventory

### Terrain
- **Contours (5 ft)** — derived from county LiDAR/DEM

### Forest / Timber Layers
- **USFS Reforestation** — FACTS activity polygons back to 1883, colored by decade of planting
- **Fire Fuel Model (FBFM40)** — LANDFIRE 2022 raster showing 40-class surface fuel model

### Infrastructure
- **Roads** — county road centerlines, server-rendered

### Species — iNaturalist (SWAP)
Research-grade observations from iNaturalist, grouped by taxon. Each species is a separate toggleable tile layer. Collapsible panel. All listed species are Michigan Species of Greatest Conservation Need (SWAP) unless noted.

| Species | Group | SWAP |
|---|---|---|
| Karner Blue Butterfly | Insects | ✓ |
| Dusted Skipper | Insects | ✓ |
| American Bumble Bee | Insects | ✓ |
| Red-headed Woodpecker | Birds | ✓ |
| Eastern Whip-poor-will | Birds | ✓ |
| Common Nighthawk | Birds | ✓ |
| Prairie Warbler | Birds | ✓ |
| Eastern Hognose Snake | Reptiles | ✓ |
| Blanding's Turtle | Reptiles | ✓ |
| Eastern Box Turtle | Reptiles | ✓ |
| Wild Lupine | Plants | — |
| Butterfly Milkweed | Plants | — |
| Sweet Crabapple | Plants | — |

Tile source: `https://api.inaturalist.org/v1/points/{z}/{x}/{y}.png?taxon_id={id}&quality_grade=research`

### Dynamic Legend
Bottom bar shows active layer legend entries — only cover types and reforestation decades currently visible in the viewport. Updates on pan/zoom and layer toggle.

### Navigation
- URL hash position sync (`#lat,lng,zoomz`) — share or bookmark any view
- GPS locate button (top-right) with live tracking and accuracy circle
- Zoom hint shown when parcels are hidden below zoom 13

### Export
- **GeoPDF** — Avenza Maps-compatible; OGC `LGIDict` georeferencing embedded (EPSG:4326 map bounds); includes open popup and legend bar
- Open popup is captured and drawn into the PDF if a parcel is selected at export time

---

## Tech Stack

No npm, no bundler — all libraries via CDN.

| Library | Version | Purpose |
|---|---|---|
| [Leaflet](https://leafletjs.com/) | 1.9.4 | Map rendering, controls |
| [esri-leaflet](https://developers.arcgis.com/esri-leaflet/) | 3.0.12 | ArcGIS FeatureServer / MapServer / ImageServer |
| [esri-leaflet-raster](https://github.com/Esri/esri-leaflet-raster) | 3.0.0 | `L.esri.imageMapLayer` for LANDFIRE rasters |
| [Turf.js](https://turfjs.org/) | 7.x | Polygon dissolve for ownership groups |
| [Leaflet.draw](https://github.com/Leaflet/Leaflet.draw) | 1.0.4 | Draw tools (points, lines, polygons) |
| [pdf-lib](https://pdf-lib.js.org/) | 1.17.1 | GeoPDF creation with embedded OGC LGIDict |
| [leaflet-image](https://github.com/mapbox/leaflet-image) | 0.4.0 | Canvas capture for PDF export |

---

## Running Locally

```bash
git clone https://github.com/karl-dykema/fieldMap.git
cd fieldMap
python3 -m http.server 8080
# open http://localhost:8080
```

No dependencies to install.

---

## Default View

Center: `43.5081, -85.7967` (Newaygo County, MI) · Zoom: 13

---

## Data Sources

| Layer | Provider | Service URL |
|---|---|---|
| Parcel records | Newaygo County GIS | `https://arcgisweb.countyofnewaygo.com/hosting/rest/services/WebMaps/PublicCountyViewerProV3/MapServer/2` |
| Soils | Newaygo County GIS | `…/PublicCountyViewerProV3/MapServer` layer 32 |
| Contours (5 ft) | Newaygo County GIS | `…/PublicCountyViewerProV3/MapServer` layer 16 |
| Roads | Newaygo County GIS | `…/PublicCountyViewerProV3/MapServer` layer 5 |
| DNR LOTS parcels | Michigan DNR (ArcGIS Online) | `https://services3.arcgis.com/Jdnp1TjADvSDxMAX/arcgis/rest/services/DNRLOTSParcelsOPENDATA/FeatureServer/0` |
| Pre-settlement vegetation | Michigan Natural Features Inventory (MNFI) | `https://services1.arcgis.com/4ezfu5dIwH83BUNL/ArcGIS/rest/services/Michigan_Land_Cover_Circa_1800/FeatureServer/0` |
| Land cover (NLCD 2021) | USGS MRLC | `https://www.mrlc.gov/geoserver/mrlc_display/NLCD_2021_Land_Cover_L48/wms` |
| Wetlands (Part 303) | Michigan EGLE / MCGI | `https://gisp.mcgi.state.mi.us/maps/rest/services/DEQ/wetlands_cache/MapServer` |
| USFS Reforestation (FACTS) | USDA Forest Service EDW | `https://apps.fs.usda.gov/arcx/rest/services/EDW/EDW_SilvicultureReforestation_01/MapServer/8` |
| Fire Fuel Model (FBFM40) | LANDFIRE 2022 (USFS / DOI) | `https://lfps.usgs.gov/arcgis/rest/services/Landfire_LF2022/LF2022_FBFM40_CONUS/ImageServer` |
| Species observations | iNaturalist (research grade) | `https://api.inaturalist.org/v1/points/{z}/{x}/{y}.png` |
| Base tiles | © OpenStreetMap contributors; Esri, Maxar, Earthstar Geographics | — |

---

## License

[GPL-3.0](LICENSE)
