# Data Directory

This directory organizes raw source files and processed outputs used by the workshop notebooks.

## Layout

```
data/
	README.md
	raw/
		wfp_food_prices_gha.csv
		wfp_food_prices_gha_qc.csv
		metadata-wfp-food-prices-for-ghana.csv
		wfp_markets_gha.csv
		geoBoundaries-GHA-ADM1.geojson
		geoBoundaries-GHA-ADM2.geojson
	processed/
		regions_with_rice_prices.geojson
```

## Required Files (Raw)

### 1. WFP Food Prices for Ghana
- Filename: `raw/wfp_food_prices_gha.csv`
- Source: [Humanitarian Data Exchange (HDX)](https://data.humdata.org/dataset/wfp-food-prices-for-ghana)
- Description: Market-level food prices for various commodities including rice
- License: Public domain / Open data

### 2. Ghana Regional Boundaries (ADM1)
- Filename: `raw/geoBoundaries-GHA-ADM1.geojson`
- Source: [geoBoundaries](https://www.geoboundaries.org/)
- Description: Polygon boundaries for Ghana's 16 regions
- License: Open data

### 3. Ghana District Boundaries (ADM2)
- Filename: `raw/geoBoundaries-GHA-ADM2.geojson`
- Source: [geoBoundaries](https://www.geoboundaries.org/)
- Description: Polygon boundaries for Ghana's districts
- License: Open data

## Processed Outputs

- `processed/regions_with_rice_prices.geojson` — Example derived dataset after spatial joins and aggregation.

## Download Instructions

1. Visit the URLs above
2. Download each file
3. Place them under `data/raw/`
4. Verify filenames match exactly as listed above

**Note:** Data files are not included in this repository due to file size constraints and to ensure you have the most up-to-date versions.
