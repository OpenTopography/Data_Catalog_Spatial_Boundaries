[![NSF-1948997](https://img.shields.io/badge/NSF-1948997-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1948997) [![NSF-1948994](https://img.shields.io/badge/NSF-1948994-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1948994) [![NSF-1948857](https://img.shields.io/badge/NSF-1948857-blue.svg)](https://nsf.gov/awardsearch/showAward?AWD_ID=1948857)

# OpenTopography Data Catalog Spatial Boundaries
Spatial boundaries of topography datasets available via OpenTopography. Combined data collection boundaries are available in GeoJSON, TopoJSON, KMZ, and GeoParquet formats. Individual dataset boundaries are available in subfolders in GeoJSON and GeoParquet formats. All boundaries are updated as new data become available in OpenTopography.

GeoJSON and TopoJSON are formats for encoding geographic data structures. GeoJSON stores geographic data in a format similar to JSON, while TopoJSON is an extension of GeoJSON that encodes topology, allowing for more compact files and efficient rendering by eliminating redundancy in shared borders between features. Both formats can be used with various GIS software and web mapping libraries.

GeoParquet is a cloud-native format that stores geographic data in Apache Parquet, a compressed, columnar file format. GeoParquet files are smaller and faster to read than GeoJSON, and can be queried directly by tools such as GeoPandas, DuckDB, GDAL, and QGIS. See [README_GeoParquet.md](README_GeoParquet.md) for the file layout and usage examples.
