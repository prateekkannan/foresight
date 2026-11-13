# Foresight — Climate Intelligence

A Next.js + TypeScript prototype for exploring global climate analogues and agricultural context. Built against the supplied Foresight / Climate Twin technical report.

## What is implemented

- Responsive dark-mode interface using the supplied Foresight dark logo; the light logo is also included in `public/` for a future light theme.
- Target-location, baseline, future-window and SSP scenario controls.
- Editable seasonal climate feature vector.
- A server-side analogue ranking endpoint using standardised Euclidean distance.
- Interactive MapLibre map and ranked analogue results.
- Regional crop/rotation/soil/water context panel with explicit caveats.
- A server-side NASA POWER daily point-data route for T2M, PRECTOTCORR and ALLSKY_SFC_SW_DWN.
- CSV export of analogue results.

## Important scientific status

This is a functional prototype, not a validated climate-risk product. The analogue candidate profiles and crop/rotation context in `lib/analogue.ts` are **illustrative demonstration records**. The displayed relative similarity index is derived from provisional feature scales; it is not a probability, calibrated confidence estimate or validated climate-match score.

The current analogue search accepts a climate feature vector and ranks the illustrative candidate set. GDD base temperature, hot-day threshold, frost definition, season boundaries and feature scaling require explicit scientific specification and validation before research use. The selected SSP and future time window are captured in the interface but are **not yet connected to NEX-GDDP-CMIP6 projection retrieval**. The manual feature vector is therefore not a model-derived future projection. Do not use these results to make farm-level decisions.

The NASA POWER route retrieves historical daily point data from the public NASA POWER API. It does not generate future projections. Before research or operational use, implement and validate a NEX-GDDP-CMIP6 ingestion pipeline, multi-model ensemble handling, bias/scale treatment, consistent temporal aggregation, source metadata and regional agricultural data connectors. See the report's NASA resource links below.

## Run locally

Requirements: Node.js 20 LTS or newer and npm.

```bash
npm install
cp .env.example .env.local
npm run dev
```

Open `http://localhost:3000`.

For production:

```bash
npm run typecheck
npm run build
npm start
```

## Deploy to Vercel

1. Push this folder to a GitHub repository.
2. Import the repository in Vercel.
3. Keep the framework preset as Next.js and use the default build command (`npm run build`).
4. No secret API key is required for the current NASA POWER public endpoint. Optionally set `NEXT_PUBLIC_MAP_STYLE` to a compatible MapLibre style URL.

## API routes

### `GET /api/health`
Returns service status.

### `GET /api/climate/power?lat=12.97&lon=77.59&start=20190101&end=20231231`
Retrieves daily NASA POWER data. Dates must use `YYYYMMDD`; supported start years are 1981 onward. Returns parameter summaries and daily values with units.

### `POST /api/analogue`
Accepts:

```json
{
  "features": {
    "precipitation": [22, 48, 115, 164],
    "gdd": 2480,
    "frostFreeDays": 345,
    "hotDays": 34,
    "rainfallVariability": 33
  },
  "location": "Bengaluru, Karnataka, India",
  "baseline": "1991–2020",
  "period": "2041–2060",
  "scenario": "SSP2-4.5"
}
```

Returns ranked candidate locations, provisional similarity scores and the largest distance contributions. All current candidate records are illustrative.

## Scientific implementation roadmap

1. **Projection ingestion:** retrieve NEX-GDDP-CMIP6 for selected coordinates, SSPs, periods and multiple GCMs. Preserve model, scenario, variable, units, calendar and spatial resolution metadata.
2. **Climate feature engineering:** compute consistent seasonal precipitation, growing degree days, frost-free days, hot-day frequency and rainfall variability for both the target projection and historical candidate regions.
3. **Comparable profiles:** define a common baseline, units, season boundaries, missing-data policy and temporal aggregation. Keep model ensemble members separate before summarising spread.
4. **Analogue ranking:** fit/justify scaling and feature weights on the intended geography and crop-climate use case. Evaluate sensitivity to distance metric and candidate sampling.
5. **Agricultural context:** connect national/regional crop statistics, crop calendars, soils and irrigation data. Avoid applying USDA datasets outside their coverage.
6. **Uncertainty and interpretation:** show model spread and scenario dependence; communicate that climate similarity is not proof of crop suitability. Surface soil, irrigation, water, market and infrastructure differences.

## Sources referenced in the project report

- NASA POWER: https://power.larc.nasa.gov/
- NASA NEX-GDDP-CMIP6: https://www.nccs.nasa.gov/services/data-collections/land-based-products/nex-gddp-cmip6
- NASA Earthdata: https://www.earthdata.nasa.gov/
- USDA Cropland Data Layer: https://www.nass.usda.gov/Research_and_Science/Cropland/
- USDA SSURGO: https://www.nrcs.usda.gov/resources/data-and-reports/soil-survey-geographic-database-ssurgo
