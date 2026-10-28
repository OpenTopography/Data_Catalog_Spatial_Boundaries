# GeoParquet boundaries

All boundaries in this repository are also published as [GeoParquet](https://geoparquet.org/) (`.parquet`) files, next to the GeoJSON files. GeoParquet stores geographic data in [Apache Parquet](https://parquet.apache.org/), a compressed, columnar file format, so the files are compact and quick to load, and tools can read only the columns and areas they need.

## What is available

| Content | GeoParquet file(s) | Geometry |
|---|---|---|
| OpenTopography point cloud datasets, combined | `OT_PC_boundaries.parquet` | simplified |
| OpenTopography raster datasets, combined | `OT_Raster_boundaries.parquet` | simplified |
| NOAA coastal lidar datasets, combined | `noaa_coastal_lidar_boundaries.parquet` | simplified |
| USGS 3DEP datasets, combined | `usgs_3dep_boundaries.parquet` | simplified |
| US Interagency Elevation Inventory, one file per layer | `USIEI/usiei_extent_layer0.parquet` … `usiei_extent_layer4.parquet` | simplified |
| Individual dataset boundaries | `OpenTopography_Point_Cloud_Lidar/<short name>.parquet`<br>`OpenTopography_Raster/<short name>.parquet`<br>`NOAA_Coastal_Lidar/<NOAA ID>.parquet`<br>`USGS_3DEP/<dataset ID>.parquet` | full detail |

Each `.parquet` file has a `.geojson` file with the same name and the same content in the same folder. As with the GeoJSON, the combined files are simplified for overview maps, and the individual files keep the full boundary. Global and national-scale raster datasets (for example SRTM, Copernicus, USGS 1 m/10 m/30 m) have individual files but are not included in `OT_Raster_boundaries`. USIEI datasets are available only in the five layer files (layer definitions are in [USIEI/README.md](USIEI/README.md)).

The files are regenerated from the OpenTopography catalog together with the GeoJSON whenever a dataset is added or updated.

## Columns

Every file has the same columns:

| Column | Type | Description |
|---|---|---|
| `id` | string | Dataset identifier (OpenTopography ID, NOAA ID, USGS dataset ID, or USIEI ID) |
| `id_type` | string | Kind of identifier: `opentopoID`, `noaaID`, `datasetId`, or `usieiID` (the GeoJSON `propertyID`) |
| `name` | string | Dataset name |
| `alternateName` | string | OpenTopography short name (OpenTopography datasets only, empty otherwise) |
| `geometry` | MultiPolygon (some individual files: Polygon) | Boundary in WGS 84 longitude/latitude (EPSG:4326) |
| `bbox` | struct (`xmin`, `ymin`, `xmax`, `ymax`) | Bounding box of each boundary, for fast spatial filtering |

Format details: GeoParquet 1.1, WKB geometry encoding, ZSTD compression, rows sorted spatially.

## Usage

Raw file URLs follow this pattern:

```
https://raw.githubusercontent.com/OpenTopography/Data_Catalog_Spatial_Boundaries/main/<path to file>
```

### Python (GeoPandas)

Requires `pip install geopandas pyarrow`.

```python
import urllib.request
import geopandas as gpd

BASE = "https://raw.githubusercontent.com/OpenTopography/Data_Catalog_Spatial_Boundaries/main"

# Download one combined file (OpenTopography raster datasets) and read it
urllib.request.urlretrieve(f"{BASE}/OT_Raster_boundaries.parquet", "OT_Raster_boundaries.parquet")
gdf = gpd.read_parquet("OT_Raster_boundaries.parquet")
print(len(gdf), "datasets")
print(gdf[["id", "name", "alternateName"]].head())

# Only the datasets that intersect a bounding box (here: California)
ca = gpd.read_parquet("OT_Raster_boundaries.parquet", bbox=(-124.5, 32.5, -114.1, 42.0))
print(len(ca), "datasets in California")

# One individual dataset boundary (full detail)
urllib.request.urlretrieve(f"{BASE}/OpenTopography_Raster/CA_HRDEM.parquet", "CA_HRDEM.parquet")
print(gpd.read_parquet("CA_HRDEM.parquet")[["id", "name"]])
```

A quick map with matplotlib (`pip install matplotlib`):

```python
import geopandas as gpd
import matplotlib.pyplot as plt

gdf = gpd.read_parquet("OT_Raster_boundaries.parquet")
ax = gdf.plot(edgecolor="darkred", facecolor="none", figsize=(10, 5))
ax.set_title("OpenTopography raster datasets")
plt.savefig("ot_raster_boundaries.png", dpi=100)
```

In a Jupyter notebook, `gdf.explore()` shows the same data on an interactive map (`pip install folium mapclassify`).

### DuckDB

```sql
SELECT id, name
FROM 'OT_Raster_boundaries.parquet'
WHERE bbox.xmin > -124.5 AND bbox.xmax < -114.1 AND bbox.ymin > 32.5 AND bbox.ymax < 42.0
ORDER BY name;
```

DuckDB can also query the file directly from its URL after `INSTALL httpfs; LOAD httpfs;`. For geometry functions, add `INSTALL spatial; LOAD spatial;`.

### GDAL and QGIS

GDAL 3.5 and later can read GeoParquet when built with the Parquet driver (check with `ogrinfo --formats | grep -i parquet`):

```bash
ogrinfo -so OT_Raster_boundaries.parquet OT_Raster_boundaries
ogr2ogr -f GPKG OT_Raster_boundaries.gpkg OT_Raster_boundaries.parquet
```

In QGIS, add a `.parquet` file with **Layer > Add Layer > Add Vector Layer**, or drag it onto the map. If QGIS does not recognize the file, its GDAL was built without the Parquet driver; a newer QGIS version usually includes it.

### Web map (OpenLayers)

GeoParquet can be read directly in the browser with [hyparquet](https://github.com/hyparam/hyparquet). Save this as an `.html` file and open it in a browser:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>OpenTopography boundaries (GeoParquet)</title>
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/ol@10.10.0/ol.css">
  <style>
    html, body, #map { margin: 0; height: 100%; }
    #info { position: absolute; top: 10px; left: 50px; background: #fff; padding: 6px 10px;
            font: 13px sans-serif; border-radius: 4px; box-shadow: 0 1px 4px rgba(0,0,0,.3); }
  </style>
</head>
<body>
  <div id="map"></div>
  <div id="info">Loading…</div>

  <!-- OpenLayers (map) + hyparquet (reads Parquet in the browser) + fzstd (ZSTD decompression) -->
  <script src="https://cdn.jsdelivr.net/npm/ol@10.10.0/dist/ol.js"></script>
  <script type="module">
    import { parquetReadObjects } from 'https://cdn.jsdelivr.net/npm/hyparquet@1.31.3/src/index.js'
    import { decompress } from 'https://cdn.jsdelivr.net/npm/fzstd@0.1.1/esm/index.mjs'

    // Any combined or individual .parquet file from this repository works here
    const PARQUET_URL = 'https://raw.githubusercontent.com/OpenTopography/Data_Catalog_Spatial_Boundaries/main/OT_Raster_boundaries.parquet'
    const info = document.getElementById('info')

    // 1. Read the GeoParquet file; hyparquet returns the geometry column as GeoJSON
    const file = await (await fetch(PARQUET_URL)).arrayBuffer()
    const rows = await parquetReadObjects({
      file,
      compressors: { ZSTD: (input, outputLength) => decompress(input, new Uint8Array(outputLength)) },
    })

    // 2. Turn each row into an OpenLayers feature (lon/lat -> Web Mercator)
    const geojson = new ol.format.GeoJSON()
    const features = rows.map(row => geojson.readFeature(
      { type: 'Feature', geometry: row.geometry, properties: { id: row.id, name: row.name } },
      { dataProjection: 'EPSG:4326', featureProjection: 'EPSG:3857' }))

    // 3. Map: OpenStreetMap basemap + the boundaries
    const boundaries = new ol.layer.Vector({
      source: new ol.source.Vector({ features }),
      style: new ol.style.Style({
        stroke: new ol.style.Stroke({ color: '#c0392b', width: 1.5 }),
        fill: new ol.style.Fill({ color: 'rgba(192, 57, 43, 0.15)' }),
      }),
    })
    const map = new ol.Map({
      target: 'map',
      layers: [new ol.layer.Tile({ source: new ol.source.OSM() }), boundaries],
      view: new ol.View({ center: [0, 0], zoom: 2 }),
    })
    map.getView().fit(boundaries.getSource().getExtent(), { padding: [40, 40, 40, 40], maxZoom: 8 })

    // 4. Click a boundary to see its name and ID
    info.textContent = `${rows.length} datasets. Click a boundary for details.`
    map.on('singleclick', evt => {
      const f = map.forEachFeatureAtPixel(evt.pixel, f => f)
      info.textContent = f ? `${f.get('name')} (${f.get('id')})` : `${rows.length} datasets. Click a boundary for details.`
    })
  </script>
</body>
</html>
```

## GeoJSON or GeoParquet?

Both contain the same boundaries. GeoJSON is plain text and works everywhere, including web maps that load a file as-is. GeoParquet is usually the better choice for analysis and for larger files: the large boundaries take roughly a third to a half of the GeoJSON size, and tools can filter by area or read single columns without loading the whole file. Very small boundaries can be slightly larger in GeoParquet, because every Parquet file carries a few kilobytes of schema and metadata.
