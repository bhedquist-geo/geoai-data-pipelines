# GeoAI Data Pipelines: Earth Engine → BigQuery → DuckDB / MotherDuck

A hands-on notebook showing where DuckDB and MotherDuck fit in a GeoAI workflow.

**Core idea:** keep large imagery in raster tools (Google Earth Engine, COG, Zarr). Reduce it to vector and tabular results (zonal statistics, point samples, features). Then use SQL for joins, validation, aggregation and sharing.

```
Earth Engine (rasters) → BigQuery / Databricks (optional) → DuckDB / MotherDuck
 reduceRegions            Export.table.toBigQuery            bronze → silver → gold → GeoParquet
```

## What's in this folder

| File | Description |
|---|---|
| `geoai_motherduck_pipeline.ipynb` | The tutorial notebook (26 cells, no saved outputs) |

## What you need

- Python 3.10+ with `duckdb`, `geopandas`, `matplotlib`, `pyarrow`
- A free [MotherDuck](https://motherduck.com) account and an access token, set as an environment variable (never paste it into the notebook):

  ```bash
  export motherduck_token="YOUR_TOKEN"
  ```

- Optional, for the real-data version: an Earth Engine account and a Google Cloud project with BigQuery enabled

## What the notebook covers

1. Where each tool fits (Earth Engine, BigQuery, Databricks, DuckDB, MotherDuck)
2. Earth Engine: reducing imagery to a county-level table (reference code)
3. Creating an isolated demo database in MotherDuck
4. Bronze layer: Texas county polygons (from a public GeoJSON) and monthly zonal statistics
5. Silver layer: validation checks and quality flags
6. Gold layer: county summary, point-in-polygon queries, and a spatial function argument-order gotcha
7. Exporting to GeoParquet and mapping the result
8. Exercises and cleanup

## What is tested, and what is not

| Part | Status |
|---|---|
| MotherDuck steps (sections 3 to 6) | SQL run successfully on a MotherDuck account, October 2026 |
| Python cells (connect, GeoParquet export, map) | Not yet run end to end. Report any issues |
| Earth Engine export (section 2) | Reference code only. Check against current Earth Engine docs |
| BigQuery / Databricks attach (section 4c) | Optional and untested. Community extension support may differ between local DuckDB and MotherDuck |

## Important: the data is synthetic

The monthly NDVI and precipitation values are a **synthetic stand-in** (tagged `SYNTHETIC_DEMO`) so the notebook runs without Earth Engine access. They follow a simple west-to-east gradient and a seasonal cycle. **They are not real measurements and should not be used for analysis.** Replace section 4b with a real export when you are ready.

County polygons come from a public GeoJSON file of US counties, filtered to Texas (254 counties).

## Notes

- All tables are created in a separate `geoai_pipeline_demo` database. The last notebook cell shows how to drop it.
- Spatial functions generally take `(lon, lat)`, but `ST_Distance_Spheroid` expects `(lat, lon)`. Passing the wrong order returns `NaN` with no error. The notebook demonstrates this.
- Do not commit tokens or saved notebook outputs that contain account details.

## License

No license has been specified yet. Until one is added, all rights are reserved by the author. If you would like to reuse this material, please open an issue.
