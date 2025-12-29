# Data Sources for Ghana Rice Affordability Analysis

This document provides a comprehensive list of datasets that can be used to enrich the base workshop analysis. All datasets are focused on Ghana and are selected for their relevance to food security, affordability, and spatial analysis.
Folder conventions: base source files are stored under `data/raw/`, derived outputs under `data/processed/`, and notebook-generated artifacts under `notebooks/output/`.

---

## Table of Contents

1. [Population & Demographics](#population--demographics)
2. [Economic Data](#economic-data)
3. [Transportation & Infrastructure](#transportation--infrastructure)
4. [Agricultural Data](#agricultural-data)
5. [Health & Nutrition](#health--nutrition)
6. [Climate & Environmental Data](#climate--environmental-data)
7. [Base Workshop Data](#base-workshop-data)

---

## Population & Demographics

### WorldPop Ghana - High-Resolution Population Density

**Description:** Gridded population density estimates at 100m resolution

**Access:**
- **URL:** https://www.worldpop.org/geodata/listing?id=79
- **Direct Download:** https://hub.worldpop.org/geodata/summary?id=24777
- **Alternative:** HDX WorldPop page - https://data.humdata.org/dataset/worldpop-population-counts-for-ghana

**Data Format:** GeoTIFF raster files

**Coverage:** 
- National coverage of Ghana
- Resolution: ~100m grid cells
- Temporal: Annual estimates (2000-2020+)

**Key Fields:**
- Population count per grid cell
- Population density (persons per sq km)

**Licensing:** Creative Commons Attribution 4.0 International (CC BY 4.0)

**Use Cases:**
- Calculate population-weighted price indices
- Identify high-population areas with high prices (priority intervention zones)
- Create affordability index: price relative to population density
- Estimate total population affected by price variations

**Example Code:**
```python
import rasterio
import geopandas as gpd

# Load population raster
with rasterio.open('data/gha_ppp_2020_1km_Aggregated_UNadj.tif') as src:
    population = src.read(1)
    transform = src.transform
```

---

### Ghana Statistical Service - Census Data

**Description:** Official census data and population statistics by district

**Access:**
- **URL:** https://statsghana.gov.gh/
- **Census Reports:** https://statsghana.gov.gh/gssmain/storage/img/marqueeupdater/Census2021_Main_Report.pdf
- **District Profiles:** Available through Ghana Open Data Initiative

**Data Format:** PDF reports, Excel files, sometimes shapefiles

**Coverage:**
- National, regional, and district levels
- Decennial census (2000, 2010, 2021)

**Key Fields:**
- Total population by district
- Household size
- Age distribution
- Urban/rural breakdown
- Literacy rates

**Licensing:** Government open data

**Use Cases:**
- District-level demographic context for price analysis
- Urban vs rural price comparisons
- Household size considerations for affordability

---

### HDX Ghana Subnational Population Statistics

**Description:** Subnational population estimates compiled by OCHA

**Access:**
- **URL:** https://data.humdata.org/dataset/ghana-subnational-population-statistics
- **Direct Download:** CSV and Excel formats available

**Data Format:** CSV, Excel

**Coverage:**
- Regional (ADM1) and district (ADM2) levels
- Regular updates

**Key Fields:**
- Total population by administrative unit
- Population projections
- Administrative codes (P-codes)

**Licensing:** Public domain / Open data

**Use Cases:**
- Quick population totals for regional/district analysis
- Population normalization of prices
- Easy-to-use tabular format for joins

**Example Code:**
```python
import pandas as pd

# Load population data
pop_data = pd.read_csv('data/ghana_subnational_population.csv')

# Merge with rice prices by district
rice_with_pop = rice_prices.merge(pop_data, on='admin2')
rice_with_pop['price_per_capita'] = rice_with_pop['price'] / rice_with_pop['population']
```

---

## Economic Data

### Ghana Living Standards Survey (GLSS) - Poverty Rates

**Description:** Comprehensive household survey including poverty and consumption data

**Access:**
- **URL:** https://statsghana.gov.gh/
- **Survey Reports:** https://www2.statsghana.gov.gh/nada/index.php/catalog/97
- **World Bank Access:** https://microdata.worldbank.org/index.php/catalog/2534

**Data Format:** Microdata (SPSS, Stata), aggregate tables (PDF, Excel)

**Coverage:**
- National, regional, and district levels
- Conducted every 5-7 years (GLSS 7 conducted 2016-2017)

**Key Fields:**
- Poverty headcount rate
- Poverty gap
- Consumption expenditure
- Food expenditure share
- Income levels

**Licensing:** Requires registration for microdata; aggregate statistics are public

**Use Cases:**
- Correlate rice prices with district poverty rates
- Identify areas where high prices coincide with high poverty
- Calculate affordability relative to income levels
- Assess food security vulnerability

---

### World Bank Ghana Economic Indicators

**Description:** Macroeconomic and regional development indicators

**Access:**
- **URL:** https://data.worldbank.org/country/ghana
- **Ghana Open Data:** https://data.worldbank.org/country/GH

**Data Format:** CSV, Excel, JSON (via API)

**Coverage:**
- National level, some regional breakdowns
- Annual time series

**Key Fields:**
- GDP per capita
- Inflation rates
- Agricultural value added
- Rural vs urban income

**Licensing:** CC BY 4.0

**Use Cases:**
- Contextualize price trends with economic indicators
- Inflation-adjusted price analysis
- Regional economic development context

---

### Market Infrastructure from OpenStreetMap

**Description:** Geographic locations of markets, shops, and commercial centers

**Access:**
- **URL:** https://www.openstreetmap.org/
- **Overpass API:** https://overpass-turbo.eu/
- **Humanitarian OSM Team:** https://export.hotosm.org/

**Data Format:** GeoJSON, Shapefile, OSM XML

**Coverage:**
- Variable completeness across Ghana
- More complete in major cities

**Query Example (Overpass Turbo):**
```
[out:json];
area["ISO3166-1"="GH"]->.ghana;
(
  node["amenity"="marketplace"](area.ghana);
  way["amenity"="marketplace"](area.ghana);
  node["shop"="supermarket"](area.ghana);
);
out center;
```

**Key Fields:**
- Market name
- Market type (formal/informal)
- Operating hours
- Coordinates

**Licensing:** ODbL (Open Database License)

**Use Cases:**
- Calculate distance to nearest market
- Analyze market density and price relationships
- Identify underserved areas
- Market access analysis

---

## Transportation & Infrastructure

### Ghana Roads Network (OpenStreetMap)

**Description:** Road network data including highways, primary, and secondary roads

**Access:**
- **URL:** https://www.openstreetmap.org/
- **Geofabrik Download:** https://download.geofabrik.de/africa/ghana.html
- **HOT Export Tool:** https://export.hotosm.org/

**Data Format:** Shapefile, GeoJSON, OSM PBF

**Coverage:**
- National coverage with varying detail
- Regular community updates

**Key Fields:**
- Road type (motorway, trunk, primary, secondary, tertiary)
- Road surface
- Road name
- Condition

**Licensing:** ODbL

**Use Cases:**
- Calculate road network density by district
- Analyze correlation between road access and prices
- Compute travel times/distances to markets
- Transportation cost modeling

**Example Code:**
```python
import osmnx as ox

# Download road network for Ghana
G = ox.graph_from_place('Ghana', network_type='drive')

# Calculate road density
gdf_nodes, gdf_edges = ox.graph_to_gdfs(G)
```

---

### Distance to Major Ports

**Description:** Ghana's major ports are in Tema and Takoradi

**Access:**
- Calculate from coordinates or use derived datasets
- Port coordinates:
  - Tema Port: 5.6167 N, 0.0167 E
  - Takoradi Port: 4.8845 N, 1.7559 W

**Use Cases:**
- Calculate distance from each district to nearest port
- Analyze how distance affects rice prices (import costs)
- Model transportation and distribution costs
- Identify inland transportation premiums

**Example Code:**
```python
from shapely.geometry import Point
import geopandas as gpd

# Port locations (longitude, latitude)
tema_port = Point(0.0167, 5.6167)
takoradi_port = Point(-1.7559, 4.8845)

# Calculate distance from each district centroid to nearest port
districts['dist_to_tema'] = districts.geometry.centroid.distance(tema_port)
districts['dist_to_takoradi'] = districts.geometry.centroid.distance(takoradi_port)
districts['dist_to_port'] = districts[['dist_to_tema', 'dist_to_takoradi']].min(axis=1)
```

---

### Market Locations from Humanitarian Data Exchange

**Description:** Curated market location data from HDX

**Access:**
- **URL:** https://data.humdata.org/dataset/ghana-market-locations
- **Alternative:** WFP market data (included in base workshop)

**Data Format:** CSV, Excel with coordinates

**Coverage:**
- Major markets across Ghana
- Curated and validated

**Key Fields:**
- Market name
- Market ID
- Latitude/longitude
- Admin1 and Admin2 codes
- Market type

**Licensing:** Public domain

**Use Cases:**
- Already included in base workshop data
- Can be enriched with additional OSM market data
- Market catchment area analysis

---

## Agricultural Data

### Crop Production Statistics (Ghana Ministry of Food & Agriculture)

**Description:** Agricultural production data by region and crop

**Access:**
- **Ministry Website:** https://mofa.gov.gh/
- **FAOSTAT Ghana:** https://www.fao.org/faostat/en/#country/81
- **Ghana Open Data:** Some datasets on data.gov.gh

**Data Format:** PDF reports, Excel files

**Coverage:**
- Regional level (ADM1)
- Annual production statistics
- Major crops including rice, maize, cassava

**Key Fields:**
- Area under cultivation
- Production volume (metric tons)
- Yield per hectare
- Crop type

**Licensing:** Government data, generally open

**Use Cases:**
- Compare rice production vs consumption patterns
- Identify rice surplus and deficit regions
- Analyze relationship between local production and prices
- Supply-demand dynamics

---

### Land Use Data

**Description:** Land cover and land use classifications

**Access:**
- **ESA WorldCover:** https://worldcover2021.esa.int/
- **Ghana Lands Commission:** https://www.ghanalandcomm.org/
- **Copernicus Land Cover:** https://land.copernicus.eu/global/

**Data Format:** GeoTIFF raster

**Coverage:**
- National coverage
- 10m resolution (ESA WorldCover)

**Key Classes:**
- Cropland
- Forest
- Built-up areas
- Grassland/savanna

**Licensing:** Open data (ESA WorldCover: CC BY 4.0)

**Use Cases:**
- Identify agricultural vs urban price patterns
- Analyze land use intensity and food prices
- Urban expansion and market dynamics

---

### Rainfall and Climate Data

**Description:** Precipitation, temperature, and climate data

**Access:**
- **Ghana Meteorological Agency:** http://www.meteo.gov.gh/
- **CHIRPS Rainfall:** https://data.chc.ucsb.edu/products/CHIRPS-2.0/
- **WorldClim:** https://www.worldclim.org/

**Data Format:** GeoTIFF raster, CSV time series

**Coverage:**
- National coverage
- Daily to monthly aggregates
- Long-term climate averages

**Key Variables:**
- Precipitation (mm)
- Temperature (C)
- Drought indices
- Growing season indicators

**Licensing:** Open data

**Use Cases:**
- Analyze seasonal price variations
- Correlate rainfall with harvest timing and prices
- Drought impact on rice prices
- Climate vulnerability assessment

**Example Code:**
```python
import xarray as xr

# Load CHIRPS rainfall data
rainfall = xr.open_dataset('chirps_ghana_2020.nc')

# Extract mean rainfall by district
district_rainfall = rainfall.groupby('district').mean()
```

---

## Health & Nutrition

### WFP Food Security Indicators

**Description:** Additional food security metrics beyond prices

**Access:**
- **WFP Data:** https://data.humdata.org/organization/wfp
- **Food Security Portal:** http://www.foodsecurityportal.org/ghana

**Data Format:** CSV, Excel

**Coverage:**
- Regional and district levels
- Regular monitoring updates

**Key Indicators:**
- Food Consumption Score (FCS)
- Dietary Diversity Score (DDS)
- Coping Strategy Index (CSI)
- Market access indicators

**Licensing:** Public domain

**Use Cases:**
- Comprehensive food security assessment
- Link prices to consumption patterns
- Identify coping mechanisms in high-price areas
- Vulnerability profiling

---

### Malnutrition Rates

**Description:** Child malnutrition and maternal nutrition data

**Access:**
- **Ghana DHS:** https://dhsprogram.com/Countries/Country-Main.cfm?ctry_id=14
- **UNICEF Ghana:** https://www.unicef.org/ghana/nutrition
- **Ghana Health Service:** https://ghs.gov.gh/

**Data Format:** PDF reports, survey microdata

**Coverage:**
- Regional level, some district data
- DHS conducted every 5 years

**Key Indicators:**
- Stunting prevalence (%)
- Wasting prevalence (%)
- Underweight prevalence (%)
- Vitamin A deficiency
- Anemia prevalence

**Licensing:** DHS requires registration; aggregate data is public

**Use Cases:**
- Link food prices to nutrition outcomes
- Identify regions where high prices correlate with malnutrition
- Target nutrition interventions
- Food security and health nexus analysis

---

## Climate & Environmental Data

### CHIRPS Rainfall Data

**Description:** Climate Hazards Group InfraRed Precipitation with Station data

**Access:**
- **Main Site:** https://www.chc.ucsb.edu/data/chirps
- **Data Portal:** https://data.chc.ucsb.edu/products/CHIRPS-2.0/

**Data Format:** GeoTIFF, NetCDF

**Coverage:**
- Daily, pentadal, monthly
- 1981-present
- 0.05 resolution (~5.5 km)

**Key Variables:**
- Precipitation (mm)
- Anomalies
- Cumulative rainfall

**Licensing:** Public domain

**Use Cases:**
- Seasonal analysis of rice prices
- Drought monitoring and price spikes
- Agricultural productivity correlations
- Climate shock analysis

---

### NDVI (Vegetation Health)

**Description:** Normalized Difference Vegetation Index from satellite imagery

**Access:**
- **MODIS NDVI:** https://lpdaac.usgs.gov/products/mod13q1v006/
- **Copernicus:** https://land.copernicus.eu/global/products/ndvi
- **Google Earth Engine:** Ready-to-use NDVI products

**Data Format:** GeoTIFF

**Coverage:**
- 16-day composites
- 250m resolution (MODIS)
- 2000-present

**Key Variables:**
- NDVI values (vegetation density/health)
- Temporal trends
- Anomalies

**Licensing:** Open data

**Use Cases:**
- Crop health monitoring
- Predict harvest quality and price impacts
- Identify agricultural stress periods
- Link vegetation health to food prices

---

## Base Workshop Data

### WFP Food Prices for Ghana

**Description:** Market-level food prices for various commodities

**Access:**
- **URL:** https://data.humdata.org/dataset/wfp-food-prices-for-ghana
- **File:** Already included in workshop (`data/raw/wfp_food_prices_gha.csv`)

**Data Format:** CSV

**Coverage:**
- 2006-present
- Major markets across Ghana
- Multiple commodities (rice, maize, cassava, etc.)

**Key Fields:**
- Date, Admin1, Admin2, Market
- Commodity, Price, Currency
- Latitude, Longitude

**Licensing:** Public domain

**Use Cases:**
- Base analysis for workshop
- Temporal price trends
- Commodity comparisons

---

### Ghana Administrative Boundaries

**Description:** Official administrative boundaries for regions and districts

**Access:**
- **geoBoundaries:** https://www.geoboundaries.org/
- **Files:** Already included in workshop
  - `data/raw/geoBoundaries-GHA-ADM1.geojson` (Regions)
  - `data/raw/geoBoundaries-GHA-ADM2.geojson` (Districts)

**Data Format:** GeoJSON, Shapefile

**Coverage:**
- ADM0 (Country), ADM1 (Regions), ADM2 (Districts)

**Key Fields:**
- Boundary geometry
- Admin names and codes
- Shape area

**Licensing:** Open data

**Use Cases:**
- Spatial joins
- Choropleth mapping
- Administrative aggregation

---

## Data Integration Tips

### General Guidelines

1. **Always check CRS (Coordinate Reference System):**
   ```python
   # Check CRS
   gdf.crs
   
   # Reproject if needed
   gdf = gdf.to_crs('EPSG:4326')  # WGS84
   ```

2. **Verify spatial alignment:**
   ```python
   # Quick visual check
   ax = boundaries.plot(color='none', edgecolor='black')
   points.plot(ax=ax, color='red', markersize=5)
   ```

3. **Handle missing data:**
   ```python
   # Check for nulls
   df.isnull().sum()
   
   # Handle appropriately
   df = df.dropna(subset=['price', 'latitude', 'longitude'])
   ```

4. **Document data sources:**
   - Keep README updated
   - Include citations in notebooks
   - Note data vintage and limitations

### Spatial Join Checklist

- [ ] Both datasets loaded successfully
- [ ] CRS matches or has been reprojected
- [ ] Geometry types are appropriate (points, polygons)
- [ ] No null geometries
- [ ] Join result checked for duplicates
- [ ] Visual validation performed

---

## Support & Questions

For questions about specific datasets or access issues:

1. Check the dataset's official documentation
2. Review the [Data Enrichment Guide](enrichment_guide.md)
3. Open an issue in this repository
4. Contact dataset providers directly

---

**Last Updated:** December 2025  
**Next Review:** March 2026

For the most current links and additional datasets, check:
- [Humanitarian Data Exchange - Ghana](https://data.humdata.org/group/gha)
- [Ghana Open Data Initiative](https://data.gov.gh/)
- [World Bank Ghana Data](https://data.worldbank.org/country/ghana)
