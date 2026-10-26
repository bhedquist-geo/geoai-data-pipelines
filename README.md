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
| `.gitignore` | Blocks credentials, keys, tokens and local data files from being committed |
| `.pre-commit-config.yaml` | Automatically clears notebook outputs before each commit (see below) |

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

## Credentials and privacy

If you adapt this notebook to your own Earth Engine, BigQuery or MotherDuck accounts, keep your private information out of any public copy:

- **Log in locally, never in the notebook.** Run `earthengine authenticate` and `gcloud auth application-default login` on your own computer, and set `motherduck_token` as an environment variable. These store credentials outside the repository.
- **Never commit secrets.** That includes tokens, `.env` files, service-account JSON keys and database files. The included `.gitignore` blocks the common ones, but it is a safety net, not a guarantee.
- **Use placeholders.** Keep `YOUR_PROJECT` in shared notebooks. A project ID is not a password, but it does identify your account.
- **Clear notebook outputs before committing.** Saved outputs can reveal project IDs, account emails, file paths and data. To do this automatically, run these once in your clone:

  ```bash
  pip install pre-commit nbstripout
  pre-commit install
  ```

  After that, outputs are stripped from notebooks every time you run `git commit`.
- **Know the limits.** The hook only runs when you commit with Git on your computer. Uploading a file through the GitHub website skips it, so clear outputs first (Jupyter: *Edit > Clear Outputs of All Cells*).
- **Keep real research data out.** Use only synthetic or public data in public repositories. Keep unpublished research in a private repository.
- **If a secret is ever committed, revoke it immediately** in Google Cloud, Earth Engine or MotherDuck. Deleting the file is not enough, because it remains in the repository history.

## Notes

- All tables are created in a separate `geoai_pipeline_demo` database. The last notebook cell shows how to drop it.
- Spatial functions generally take `(lon, lat)`, but `ST_Distance_Spheroid` expects `(lat, lon)`. Passing the wrong order returns `NaN` with no error. The notebook demonstrates this.

## License

No license has been specified yet. Until one is added, all rights are reserved by the author. If you would like to reuse this material, please open an issue.
