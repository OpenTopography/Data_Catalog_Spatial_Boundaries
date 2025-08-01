# USIEI Dataset Extent Boundaries (Layers 0–4)
US Interagency Elevation Inventory

The U.S. Interagency Elevation Inventory shows availability of high-accuracy topographic and bathymetric data for the United States and its territories. The project is a collaborative effort between NOAA and the U.S. Geological Survey, with contributions from the Federal Emergency Management Agency, U.S. Department of Agriculture, and U.S. Army Corps of Engineers. This resource is a comprehensive, nationwide listing of known high-accuracy topographic data, including lidar and IfSAR, and bathymetric data, including NOAA hydrographic surveys, multibeam data, and bathymetric lidar. OpenTopography federates access to the USIEI as a service to our users. Visit United States Interagency Elevation Inventory to learn more about the USIEI

This repository contains the spatial extent boundaries for datasets in the **United States Interagency Elevation Inventory (USIEI)**. Each dataset is grouped into one of five standardized layers based on its data type.

For each layer, we provide three formats:
- `.geojson` — standard format for use in GIS, web mapping
- `.topojson` — compact format optimized for web visualization
- `.kmz` — for use in Google Earth (compressed KML)

---

## 📚 Layer Definitions

| Layer Index | Description                      |
|-------------|----------------------------------|
| **0**       | Topobathy Shoreline Lidar        |
| **1**       | Topographic Lidar                |
| **2**       | Bathymetric Lidar                |
| **3**       | IfSAR (Interferometric SAR)      |
| **4**       | Other Bathymetric Surveys        |

---

## 📁 Available Files

### Layer 0 – Topobathy Shoreline Lidar
- [usiei_extent_layer0.geojson](./usiei_extent_layer0.geojson)
- [usiei_extent_layer0.topojson](./usiei_extent_layer0.topojson)
- [usiei_extent_layer0.kmz](./usiei_extent_layer0.kmz)

---

### Layer 1 – Topographic Lidar
- [usiei_extent_layer1.geojson](./usiei_extent_layer1.geojson)
- [usiei_extent_layer1.topojson](./usiei_extent_layer1.topojson)
- [usiei_extent_layer1.kmz](./usiei_extent_layer1.kmz)

---

### Layer 2 – Bathymetric Lidar
- [usiei_extent_layer2.geojson](./usiei_extent_layer2.geojson)
- [usiei_extent_layer2.topojson](./usiei_extent_layer2.topojson)
- [usiei_extent_layer2.kmz](./usiei_extent_layer2.kmz)

---

### Layer 3 – IfSAR
- [usiei_extent_layer3.geojson](./usiei_extent_layer3.geojson)
- [usiei_extent_layer3.topojson](./usiei_extent_layer3.topojson)
- [usiei_extent_layer3.kmz](./usiei_extent_layer3.kmz)

---

### Layer 4 – Other Bathymetric Surveys
- [usiei_extent_layer4.geojson](./usiei_extent_layer4.geojson)
- [usiei_extent_layer4.topojson](./usiei_extent_layer4.topojson)
- [usiei_extent_layer4.kmz](./usiei_extent_layer4.kmz)

---

## 📌 Notes

- These boundaries represent the **extents of individual USIEI datasets**, not the full coverage area of all data.
- File sizes have been optimized for web use where possible.
- The KMZ files can be opened in **Google Earth Pro** and are styled for visual clarity.
- The TopoJSON files are suitable for lightweight rendering in JavaScript libraries such as [D3.js](https://d3js.org/) or [MapLibre](https://maplibre.org/).

---

## 📞 Contact

For more information or questions about these files, please contact:
OpenTopography, email info@opentopography.org   
