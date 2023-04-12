# Exploratory Serverless Webmapping Work from NYS Cities Program
Repo for some exploratory/experimental serverless webmapping work. This is intended to develop some proof-of-concept work for showing large spatial datasets in webmaps without relying infrastructure beyond basic webserver functionality (e.g., in GitHub pages, Netlify, or similar) and bucket file storage (e.g., S3).

Questions about this work should be directed to Mike Treglia, Lead Scientist of the NY Cities Program at The Nature Conservancy at [michael.treglia@tnc.org](mailto:michael.treglia@tnc.org)

## Vector Example using PMTiles
This example shows a full dataset of the estimated opportunity for new tree canopy in NYC. The vector dataset displayed is the actual practical canopy data layer availble from [Zenodo](https://zenodo.org/record/6547492), described in a [*Frontiers in Sustainable Cities* paper](https://www.frontiersin.org/articles/10.3389/frsc.2022.944823/full). 

This work relies on the [**Protomaps**](https://protomaps.com/) project, in particular, an associated format for storing tiled vector data, [PMTiles](https://protomaps.com/docs/pmtiles). Descriptions of how this PMTiles can be used is documented at the links above and in the associated GitHub repository.

In this work, key steps involved so far have been:
1.  Converet the vector dataset to a format suitable for the vector tiling tool, tippecanoe. In this case, we converted from a gpkg file to  geojson using the command line [ogr2ogr](https://gdal.org/programs/ogr2ogr.html) tool in [gdal](https://gdal.org/):

    `ogr2ogr -t_srs EPSG:4326 nyc_practicalcanopy.json nyc_practicalcanopy.gpkg nyc_practicalcanopy`

2. Convert the geojson file to a tiled vector dataset in the *pmtiles* format using [tippecanoe](https://github.com/felt/tippecanoe):

    `tippecanoe -zg -P --drop-densest-as-needed --projection=EPSG:4326 -o nyc_practicalcanopy.pmtiles -l nyc_practicalcanopy nyc_practicalcanopy.json`
    
3. Leverage the [PMTiles javascript library](https://www.npmjs.com/package/pmtiles) in conjunction with [leaflet.js](https://leafletjs.com/) for webmapping to put together an interactive map. This can leverage local files, or files in a cloud storage bucket.
    

## Raster Example using Cloud Optimized Geotiffs

This example shows some of the [high resolution land cover data for NYC from 2017](https://data.cityofnewyork.us/Environment/Land-Cover-Raster-Data-2017-6in-Resolution/he6d-2qns), initially for a small area. 

This work relies on the cloud-optimized specification of the geotiff format ([**Cloud Optimized Geotiff a.k.a. COG**](https://www.cogeo.org/)), and the [georaster-layer-for-leaflet](https://github.com/geotiff/georaster-layer-for-leaflet) (in combination with [leaflet.js](https://leafletjs.com/) for the webmapping).

In terms of steps used to achieve the working proof-of-concept, the landcover data were downloaded from the NYC Open Data Portal, and a small sample area was extracted and exported using [QGIS](https://qgis.org/). In terms of processing the data and putting together the webmap the general steps look like::

1. Convert file into a COG using [`gdal_translate`](https://gdal.org/programs/gdal_translate.html). 

  `gdal_translate sample_4326.tif -o sample_4326_cog.tif -of COG`
  * Note - initial attempts using Lat/Long coordinates (EPSG 4326) were not successful with the webmap, so [`gdal_warp`](https://gdal.org/programs/gdalwarp.html) was used to reproject the data.
  
2. Leverage [georaster-layer-for-leaflet](https://github.com/geotiff/georaster-layer-for-leaflet) (in combination with [leaflet.js](https://leafletjs.com/) for the webmapping.


