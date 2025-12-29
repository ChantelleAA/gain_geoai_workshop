# Data Enrichment Guide for GeoAI Analysis

This guide provides a comprehensive workflow for enriching the base Ghana rice affordability analysis with additional datasets. It's designed for workshop participants who want to extend their analysis beyond the core content.

---

## Table of Contents

1. [What is Data Enrichment?](#what-is-data-enrichment)
2. [Why Enrich Spatial Data?](#why-enrich-spatial-data)
3. [The Enrichment Workflow](#the-enrichment-workflow)
4. [Code Examples](#code-examples)
5. [Common Challenges & Solutions](#common-challenges--solutions)
6. [Best Practices](#best-practices)
7. [Example Research Questions](#example-research-questions)

---

## What is Data Enrichment?

**Data enrichment** is the process of enhancing your primary dataset by integrating additional contextual information from complementary data sources. In geospatial analysis, this typically involves:

- **Spatial Joins:** Combining datasets based on geographic relationships
- **Attribute Enhancement:** Adding new variables to existing spatial features
- **Derived Metrics:** Calculating new indicators from multiple data sources
- **Contextual Layering:** Building a more complete picture of spatial patterns

### Example

In the base workshop, you analyze rice prices across Ghana's districts. Data enrichment would add:
- Population density  Calculate affordability per capita
- Poverty rates  Identify vulnerable populations
- Market locations  Assess market access
- Road networks  Analyze transportation costs

---

## Why Enrich Spatial Data?

### 1. **Deeper Policy Insights**

Raw price data alone shows *where* prices are high. Enriched data answers *why* and *who is affected*:
- High prices + high population = many people affected
- High prices + high poverty = affordability crisis
- High prices + poor roads = transportation barriers

### 2. **Evidence-Based Decision Making**

Policy makers need context to prioritize interventions:
- Should subsidies target high-price regions or high-poverty regions?
- Where would improved roads have the biggest impact on prices?
- Which markets should be expanded first?

### 3. **Correlation and Causation Analysis**

Enrichment enables statistical analysis of relationships:
- Do better roads correlate with lower prices?
- Is there a relationship between population density and price volatility?
- How does distance from ports affect rice prices?

### 4. **Comprehensive Food Security Assessment**

Food security isn't just about pricesit's about:
- Access (market proximity, transportation)
- Availability (production, imports)
- Utilization (nutrition outcomes)
- Stability (price volatility, climate shocks)

---

## The Enrichment Workflow

### High-Level Process

```
1. Define Research Question
   
2. Identify Relevant Data Sources
   
3. Acquire and Validate Data
   
4. Check Spatial Compatibility
   
5. Perform Spatial Operations
   
6. Calculate Derived Metrics
   
7. Analyze and Visualize
   
8. Interpret and Communicate
```

### Detailed Steps

#### Step 1: Define Your Research Question

**Start with a clear question** that enrichment can help answer:

Good questions:
-  "Which districts have both high rice prices AND high poverty rates?"
-  "How does distance to ports correlate with rice prices?"
-  "Are densely populated areas adequately served by markets?"

Too vague:
-  "Tell me about rice in Ghana"
-  "What's interesting about the data?"

#### Step 2: Identify Relevant Enrichment Datasets

For your research question, determine what additional data you need:

| Research Question | Needed Data |
|-------------------|-------------|
| Price-poverty correlation | Poverty rates by district (GLSS) |
| Population-affected analysis | Population density (WorldPop) |
| Market access impact | Market locations (OSM), roads |
| Import cost analysis | Port locations, road networks |
| Climate impact on prices | Rainfall data (CHIRPS), NDVI |

**Refer to** [`data_sources.md`](data_sources.md) for comprehensive dataset listings.

Note: Base workshop data is organized under `data/raw/` and processed outputs under `data/processed/`. Notebook-generated artifacts should be saved in `notebooks/output/`.

#### Step 3: Acquire and Validate Data

**Download the data:**
- Follow instructions in `data_sources.md`
- Save to `data/enrichment/` directory
- Keep original filenames or use descriptive names

**Initial validation:**
```python
import geopandas as gpd
import pandas as pd

# Load and inspect
enrichment_data = gpd.read_file('data/enrichment/population_density.geojson')

print(f"Shape: {enrichment_data.shape}")
print(f"CRS: {enrichment_data.crs}")
print(f"Columns: {enrichment_data.columns.tolist()}")
print(f"Null values:\n{enrichment_data.isnull().sum()}")

# Preview
enrichment_data.head()
```

#### Step 4: Check Spatial Compatibility

**Coordinate Reference Systems (CRS) must match:**

```python
# Check CRS
print(f"Rice data CRS: {rice_gdf.crs}")
print(f"Enrichment CRS: {enrichment_data.crs}")

# Reproject if needed
if rice_gdf.crs != enrichment_data.crs:
    enrichment_data = enrichment_data.to_crs(rice_gdf.crs)
    print(" CRS aligned")
```

**Common CRS for Ghana:**
- **EPSG:4326** (WGS84) - latitude/longitude, good for global datasets
- **EPSG:32630** (UTM Zone 30N) - meters, good for Ghana-specific distance calculations

**Visual validation:**
```python
import matplotlib.pyplot as plt

fig, ax = plt.subplots(figsize=(10, 8))
enrichment_data.plot(ax=ax, color='lightblue', edgecolor='black', alpha=0.5)
rice_gdf.plot(ax=ax, color='red', markersize=5)
plt.title("Spatial Alignment Check")
plt.show()
```

#### Step 5: Perform Spatial Operations

**Common spatial operations:**

**A. Spatial Join (Point-in-Polygon)**

Join rice price points to administrative boundaries:

```python
# Each price point gets attributes from the polygon it falls within
rice_with_districts = gpd.sjoin(
    rice_gdf,  # points
    districts_gdf,  # polygons
    how='left',
    predicate='within'
)
```

**B. Buffer Analysis**

Create buffers around points:

```python
# Create 5km buffer around each market
markets_buffered = markets_gdf.copy()
markets_buffered['geometry'] = markets_gdf.geometry.buffer(0.05)  # ~5km in degrees

# Count population within buffer
population_in_buffer = gpd.sjoin(
    population_points,
    markets_buffered,
    how='inner',
    predicate='within'
)
```

**C. Nearest Neighbor Analysis**

Find closest feature:

```python
from shapely.ops import nearest_points

def find_nearest_market(district_centroid, markets_gdf):
    """Find nearest market to a district centroid"""
    nearest_geom = markets_gdf.distance(district_centroid).idxmin()
    nearest_market = markets_gdf.loc[nearest_geom]
    distance = district_centroid.distance(nearest_market.geometry)
    return nearest_market['market_name'], distance

# Apply to all districts
districts_gdf['nearest_market'] = districts_gdf.geometry.centroid.apply(
    lambda x: find_nearest_market(x, markets_gdf)[0]
)
districts_gdf['dist_to_market'] = districts_gdf.geometry.centroid.apply(
    lambda x: find_nearest_market(x, markets_gdf)[1]
)
```

**D. Aggregation by Area**

Aggregate point data to polygons:

```python
# Calculate mean rice price per district
district_prices = rice_with_districts.groupby('district_name').agg({
    'price': ['mean', 'median', 'std', 'count']
}).reset_index()

district_prices.columns = ['district_name', 'mean_price', 'median_price', 'std_price', 'n_observations']
```

#### Step 6: Calculate Derived Metrics

Create new indicators from multiple data sources:

**Affordability Index:**
```python
# Lower index = less affordable (high price, low income)
districts_enriched['affordability_index'] = (
    districts_enriched['median_income'] / districts_enriched['median_price']
)
```

**Population-Weighted Price:**
```python
# Gives more weight to high-population areas
weighted_avg_price = (
    districts_enriched['median_price'] * districts_enriched['population']
).sum() / districts_enriched['population'].sum()
```

**Price Impact Score:**
```python
# Combines price and population to identify high-impact areas
districts_enriched['impact_score'] = (
    districts_enriched['median_price'] * 
    districts_enriched['population'] / 1000  # normalize
)
```

**Market Access Score:**
```python
# Lower score = worse access
districts_enriched['market_access'] = 1 / (1 + districts_enriched['dist_to_market'])
```

#### Step 7: Analyze and Visualize

**Correlation Analysis:**
```python
import seaborn as sns

# Correlation matrix
correlation_vars = ['median_price', 'population_density', 'poverty_rate', 'dist_to_port']
correlation_matrix = districts_enriched[correlation_vars].corr()

# Heatmap
plt.figure(figsize=(10, 8))
sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm', center=0)
plt.title("Correlation Between Rice Prices and Contextual Factors")
plt.tight_layout()
plt.show()
```

**Scatter Plots:**
```python
fig, axes = plt.subplots(1, 2, figsize=(14, 5))

# Price vs Population Density
axes[0].scatter(
    districts_enriched['population_density'], 
    districts_enriched['median_price'],
    alpha=0.6
)
axes[0].set_xlabel('Population Density (persons/km)')
axes[0].set_ylabel('Median Rice Price (GHS)')
axes[0].set_title('Price vs Population Density')

# Price vs Poverty Rate
axes[1].scatter(
    districts_enriched['poverty_rate'], 
    districts_enriched['median_price'],
    alpha=0.6,
    color='coral'
)
axes[1].set_xlabel('Poverty Rate (%)')
axes[1].set_ylabel('Median Rice Price (GHS)')
axes[1].set_title('Price vs Poverty Rate')

plt.tight_layout()
plt.show()
```

**Multi-Variable Choropleth:**
```python
fig, axes = plt.subplots(1, 3, figsize=(18, 5))

# Price
districts_enriched.plot(
    column='median_price',
    cmap='YlOrRd',
    legend=True,
    ax=axes[0]
)
axes[0].set_title('Median Rice Price')
axes[0].axis('off')

# Population Density
districts_enriched.plot(
    column='population_density',
    cmap='YlGnBu',
    legend=True,
    ax=axes[1]
)
axes[1].set_title('Population Density')
axes[1].axis('off')

# Affordability Index
districts_enriched.plot(
    column='affordability_index',
    cmap='RdYlGn',  # Red=less affordable, Green=more affordable
    legend=True,
    ax=axes[2]
)
axes[2].set_title('Affordability Index')
axes[2].axis('off')

plt.tight_layout()
plt.show()
```

#### Step 8: Interpret and Communicate

**Key questions to answer:**

1. **What patterns emerge?**
   - "Districts in the northern region show both high prices and high poverty rates"

2. **Are there correlations?**
   - "We observe a moderate positive correlation (r=0.45) between distance to port and rice prices"

3. **Who is most affected?**
   - "An estimated 2.3 million people live in districts with both above-average prices and below-average incomes"

4. **What are the policy implications?**
   - "Priority intervention areas should include [specific districts] where affordability is lowest"

5. **What are the limitations?**
   - "Analysis uses 2020 population data; recent migration patterns may affect accuracy"

---

## Code Examples

### Complete Enrichment Example: Population Density

```python
import geopandas as gpd
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np

# 1. Load base data (from workshop)
rice_prices = pd.read_csv('data/raw/wfp_food_prices_gha.csv')
rice_prices = rice_prices[rice_prices['commodity'].str.contains('Rice', case=False)]

# Convert to GeoDataFrame
from shapely.geometry import Point
rice_gdf = gpd.GeoDataFrame(
    rice_prices,
    geometry=[Point(xy) for xy in zip(rice_prices['longitude'], rice_prices['latitude'])],
    crs='EPSG:4326'
)

# Load district boundaries
districts = gpd.read_file('data/raw/geoBoundaries-GHA-ADM2.geojson')

# 2. Load enrichment data (population)
# For this example, assume we have district-level population data
population_data = pd.read_csv('data/enrichment/ghana_district_population.csv')
# Columns: district_name, population, area_km2

# 3. Spatial join: add district info to price points
rice_with_districts = gpd.sjoin(rice_gdf, districts, how='left', predicate='within')

# 4. Aggregate prices by district
district_stats = rice_with_districts.groupby('shapeName').agg({
    'price': ['mean', 'median', 'std', 'count']
}).reset_index()
district_stats.columns = ['district_name', 'mean_price', 'median_price', 'std_price', 'n_obs']

# 5. Merge with population data
districts_enriched = districts.merge(district_stats, left_on='shapeName', right_on='district_name', how='left')
districts_enriched = districts_enriched.merge(population_data, left_on='shapeName', right_on='district_name', how='left')

# 6. Calculate derived metrics
districts_enriched['pop_density'] = districts_enriched['population'] / districts_enriched['area_km2']
districts_enriched['people_affected_high_price'] = np.where(
    districts_enriched['median_price'] > districts_enriched['median_price'].median(),
    districts_enriched['population'],
    0
)

# 7. Visualize
fig, axes = plt.subplots(1, 2, figsize=(16, 6))

# Median price map
districts_enriched.plot(
    column='median_price',
    cmap='RdYlGn_r',
    legend=True,
    ax=axes[0],
    edgecolor='black',
    linewidth=0.5,
    missing_kwds={'color': 'lightgrey'}
)
axes[0].set_title('Median Rice Price by District', fontsize=14)
axes[0].axis('off')

# Population density map
districts_enriched.plot(
    column='pop_density',
    cmap='YlOrRd',
    legend=True,
    ax=axes[1],
    edgecolor='black',
    linewidth=0.5,
    missing_kwds={'color': 'lightgrey'}
)
axes[1].set_title('Population Density by District', fontsize=14)
axes[1].axis('off')

plt.tight_layout()
plt.savefig('notebooks/output/enrichment_maps.png', dpi=300, bbox_inches='tight')
plt.show()

# 8. Summary statistics
print("\n=== ENRICHMENT ANALYSIS SUMMARY ===")
print(f"Total districts: {len(districts_enriched)}")
print(f"Districts with price data: {districts_enriched['median_price'].notna().sum()}")
print(f"Total population in analysis: {districts_enriched['population'].sum():,.0f}")
print(f"Population in high-price districts: {districts_enriched['people_affected_high_price'].sum():,.0f}")

# Correlation
corr = districts_enriched[['median_price', 'pop_density']].corr().iloc[0, 1]
print(f"\nCorrelation between price and population density: {corr:.3f}")

# Top priority districts (high price + high population)
districts_enriched['priority_score'] = (
    districts_enriched['median_price'] * districts_enriched['pop_density']
)
top_priority = districts_enriched.nlargest(5, 'priority_score')[['shapeName', 'median_price', 'pop_density', 'population']]
print("\nTop 5 Priority Districts (High Price + High Density):")
print(top_priority.to_string(index=False))
```

---

## Common Challenges & Solutions

### Challenge 1: CRS Mismatch

**Symptom:** Spatial join produces no results or incorrect matches

**Solution:**
```python
# Always check and align CRS
print(f"Dataset A CRS: {gdf_a.crs}")
print(f"Dataset B CRS: {gdf_b.crs}")

# Reproject to match
gdf_b = gdf_b.to_crs(gdf_a.crs)

# Or reproject both to a standard CRS
gdf_a = gdf_a.to_crs('EPSG:4326')
gdf_b = gdf_b.to_crs('EPSG:4326')
```

---

### Challenge 2: Name Mismatches in Joins

**Symptom:** Tabular join fails to match records due to spelling differences

**Example:** "Accra" vs "ACCRA" vs "Accra Metropolis"

**Solution:**
```python
# Standardize names
def standardize_name(name):
    return name.strip().upper().replace('  ', ' ')

df1['district_clean'] = df1['district'].apply(standardize_name)
df2['district_clean'] = df2['district'].apply(standardize_name)

# Join on cleaned names
merged = df1.merge(df2, on='district_clean', how='left')

# Check for unmatched
unmatched = merged[merged['population'].isna()]
if len(unmatched) > 0:
    print("Unmatched districts:")
    print(unmatched['district'].tolist())
```

**Advanced:** Use fuzzy matching for tricky cases
```python
from fuzzywuzzy import process

def fuzzy_match(name, choices, threshold=80):
    match, score = process.extractOne(name, choices)
    return match if score >= threshold else None

# Apply fuzzy matching
df1['matched_name'] = df1['district'].apply(
    lambda x: fuzzy_match(x, df2['district'].tolist())
)
```

---

### Challenge 3: Missing Data

**Symptom:** NaN values after spatial join or merge

**Solutions:**

**Identify extent:**
```python
# Check missing values
print(enriched_data.isnull().sum())

# Identify which districts lack data
missing = enriched_data[enriched_data['population'].isna()]
print(f"Districts without population data: {missing['district_name'].tolist()}")
```

**Handle appropriately:**
```python
# Option 1: Drop rows with missing critical variables
cleaned = enriched_data.dropna(subset=['median_price', 'population'])

# Option 2: Fill with reasonable defaults
enriched_data['population'] = enriched_data['population'].fillna(enriched_data['population'].median())

# Option 3: Flag and keep
enriched_data['has_population_data'] = enriched_data['population'].notna()

# Option 4: Interpolate from neighbors (advanced)
# Use spatial interpolation methods
```

---

### Challenge 4: Scale and Resolution Differences

**Symptom:** Point data at market level, but enrichment data at regional level

**Solution:**

**Aggregate up:**
```python
# Aggregate fine-grained points to coarser regions
regional_avg = market_prices.groupby('region').agg({
    'price': 'mean',
    'observations': 'sum'
}).reset_index()

# Join with regional-level enrichment data
regional_enriched = regions.merge(regional_avg, on='region')
```

**Disaggregate down (with caution):**
```python
# Spatially join to assign regional values to all points in that region
points_with_regional = gpd.sjoin(
    market_points, 
    regions[['region_name', 'poverty_rate', 'geometry']], 
    how='left',
    predicate='within'
)
# Note: This assumes regional values apply uniformly (often not true!)
```

---

### Challenge 5: Large Raster Data

**Symptom:** Population raster is huge and slow to process

**Solution:**

**Clip to area of interest:**
```python
import rasterio
from rasterio.mask import mask

# Load Ghana boundary
ghana = gpd.read_file('data/raw/geoBoundaries-GHA-ADM0.geojson')

# Clip raster to Ghana
with rasterio.open('data/worldpop_full.tif') as src:
    out_image, out_transform = mask(src, ghana.geometry, crop=True)
    out_meta = src.meta.copy()
    out_meta.update({
        'height': out_image.shape[1],
        'width': out_image.shape[2],
        'transform': out_transform
    })

# Save clipped raster
with rasterio.open('data/worldpop_ghana.tif', 'w', **out_meta) as dest:
    dest.write(out_image)
```

**Aggregate to coarser resolution:**
```python
# Resample to lower resolution
from rasterio.enums import Resampling

with rasterio.open('data/worldpop_ghana.tif') as src:
    # Read at 1/10 resolution (10x10 pixel aggregation)
    data = src.read(
        out_shape=(src.count, int(src.height / 10), int(src.width / 10)),
        resampling=Resampling.average
    )
```

---

## Best Practices

### 1. Document Everything

**Keep a data log:**
```python
# At the top of your notebook
"""
DATA SOURCES:
- Rice prices: WFP HDX, downloaded 2025-12-15
- Population: WorldPop 2020, accessed 2025-12-20
- Boundaries: geoBoundaries v5.0
- Poverty: GLSS Round 7 (2016-2017)

LIMITATIONS:
- Population data is from 2020; may not reflect recent changes
- Poverty data is from 2016-2017; Ghana has since been reclassified
- Market prices vary significantly within districts
"""
```

### 2. Validate at Each Step

```python
# After every major operation
print(f"Shape before: {df_before.shape}")
print(f"Shape after: {df_after.shape}")
print(f"Null values: {df_after.isnull().sum().sum()}")

# Visual validation
df_after.plot()
plt.show()
```

### 3. Use Version Control

```bash
# Track data provenance
git add data/enrichment/population_2020.csv
git commit -m "Add WorldPop 2020 population data for districts"

# Track analysis scripts
git add notebooks/enrichment_analysis.ipynb
git commit -m "Add population density enrichment analysis"
```

### 4. Create Reproducible Workflows

```python
# Use functions for reusability
def enrich_with_population(price_gdf, population_data, district_boundaries):
    """
    Enrich price data with population information
    
    Parameters:
    -----------
    price_gdf : GeoDataFrame
        Price points with geometry
    population_data : DataFrame
        Population by district
    district_boundaries : GeoDataFrame
        District polygons
        
    Returns:
    --------
    GeoDataFrame
        Enriched data with population metrics
    """
    # Implementation
    pass

# Use it
enriched = enrich_with_population(rice_gdf, pop_data, districts)
```

### 5. Consider Temporal Alignment

```python
# Check data vintage
print(f"Price data: {rice_prices['date'].min()} to {rice_prices['date'].max()}")
print(f"Population data: 2020")
print(f"Poverty data: 2016-2017")

# Filter price data to match temporal scope if needed
recent_prices = rice_prices[rice_prices['date'] >= '2020-01-01']
```

### 6. Normalize and Standardize

```python
# For fair comparisons across different scales
from sklearn.preprocessing import StandardScaler

# Z-score normalization
scaler = StandardScaler()
districts_enriched[['price_z', 'pop_z', 'poverty_z']] = scaler.fit_transform(
    districts_enriched[['median_price', 'population', 'poverty_rate']]
)

# Now can calculate composite scores
districts_enriched['vulnerability_score'] = (
    districts_enriched['price_z'] + 
    districts_enriched['poverty_z'] - 
    districts_enriched['pop_z']  # negative because higher pop = more people affected
)
```

### 7. Report Uncertainty

```python
# Calculate and report confidence metrics
districts_enriched['price_cv'] = (
    districts_enriched['std_price'] / districts_enriched['mean_price']
)

# Flag low-confidence estimates
districts_enriched['reliable'] = (
    (districts_enriched['n_obs'] >= 10) & 
    (districts_enriched['price_cv'] < 0.5)
)

print(f"Reliable estimates: {districts_enriched['reliable'].sum()} / {len(districts_enriched)}")
```

---

## Example Research Questions

Here are specific research questions you can answer with data enrichment, along with the datasets needed:

### 1. Price-Population Impact Analysis

**Question:** *"Which districts have the highest number of people affected by above-average rice prices?"*

**Datasets:**
- WFP rice prices (base)
- District population (WorldPop or HDX)
- District boundaries (geoBoundaries)

**Approach:**
1. Calculate median price across all districts
2. Identify districts with above-median prices
3. Multiply excess price by population
4. Rank districts by total impact

**Code Skeleton:**
```python
districts['price_excess'] = districts['median_price'] - districts['median_price'].median()
districts['population_impact'] = districts['price_excess'] * districts['population']
top_impact = districts.nlargest(10, 'population_impact')
```

---

### 2. Affordability-Poverty Analysis

**Question:** *"Where are rice prices least affordable relative to poverty levels?"*

**Datasets:**
- WFP rice prices
- GLSS poverty rates
- District boundaries

**Approach:**
1. Normalize prices and poverty rates
2. Create affordability index: poverty rate / median price
3. Identify districts with high poverty AND high prices

**Code Skeleton:**
```python
districts['affordability_crisis'] = (
    districts['poverty_rate_z'] + districts['median_price_z']
) / 2
crisis_districts = districts[districts['affordability_crisis'] > 1.5]
```

---

### 3. Market Access Analysis

**Question:** *"How does distance to markets affect rice prices?"*

**Datasets:**
- WFP market locations
- District boundaries
- OSM road network (optional)

**Approach:**
1. Calculate distance from district centroid to nearest market
2. Correlate distance with median prices
3. Account for road quality if available

**Code Skeleton:**
```python
from scipy.stats import pearsonr

# Calculate correlation
corr, p_value = pearsonr(
    districts['dist_to_market'], 
    districts['median_price']
)
print(f"Correlation: {corr:.3f} (p={p_value:.4f})")
```

---

### 4. Import Cost Analysis

**Question:** *"Do inland districts pay more for rice due to transportation from ports?"*

**Datasets:**
- WFP rice prices
- Port locations (Tema, Takoradi)
- Road network

**Approach:**
1. Calculate distance from each district to nearest port
2. Regress price on distance
3. Estimate per-kilometer cost

**Code Skeleton:**
```python
from scipy.stats import linregress

slope, intercept, r_value, p_value, std_err = linregress(
    districts['dist_to_port'], 
    districts['median_price']
)
print(f"Each 100km from port adds {slope*100:.2f} GHS to price")
```

---

### 5. Climate-Price Relationships

**Question:** *"Do rice prices increase during dry seasons?"*

**Datasets:**
- WFP rice prices (temporal)
- CHIRPS rainfall data

**Approach:**
1. Extract monthly rainfall for each district
2. Group prices by month
3. Correlate rainfall and prices with lag

**Code Skeleton:**
```python
# Add month to prices
prices['month'] = pd.to_datetime(prices['date']).dt.month

# Monthly averages
monthly_stats = prices.groupby('month').agg({
    'price': 'mean',
    'rainfall': 'mean'
}).reset_index()

# Correlation
corr = monthly_stats[['price', 'rainfall']].corr().iloc[0, 1]
```

---

### 6. Production-Consumption Gap

**Question:** *"Do rice-producing regions have lower prices than consuming regions?"*

**Datasets:**
- WFP rice prices
- MOFA crop production statistics

**Approach:**
1. Classify regions as net producers or consumers
2. Compare mean prices between groups
3. Statistical test (t-test)

**Code Skeleton:**
```python
from scipy.stats import ttest_ind

producer_prices = districts[districts['rice_production'] > districts['rice_production'].median()]['median_price']
consumer_prices = districts[districts['rice_production'] <= districts['rice_production'].median()]['median_price']

t_stat, p_value = ttest_ind(producer_prices, consumer_prices)
print(f"Producers: {producer_prices.mean():.2f}, Consumers: {consumer_prices.mean():.2f}")
print(f"Difference is significant: {p_value < 0.05}")
```

---

## Next Steps

### For Workshop Participants

1. **Try the example notebook:** Work through `notebooks/02_data_enrichment_example.ipynb`
2. **Pick a research question:** Choose one from above or create your own
3. **Download data:** Use `data_sources.md` to find datasets
4. **Follow this guide:** Use the workflow and code examples
5. **Share your findings:** Create a new notebook with your analysis

### For Instructors

- Use this guide as a reference during Q&A
- Suggest specific enrichment projects for advanced students
- Encourage students to present enrichment findings
- Consider adding a "show and tell" session for extensions

### For Researchers

- Adapt these methods to your specific research questions
- Combine multiple enrichment datasets for comprehensive analysis
- Publish reproducible workflows
- Contribute new examples back to this repository

---

## Additional Resources

### Tutorials
- [GeoPandas Spatial Joins](https://geopandas.org/en/stable/gallery/spatial_joins.html)
- [Spatial Analysis with PySAL](https://pysal.org/notebooks/intro.html)
- [Rasterio for Raster Data](https://rasterio.readthedocs.io/)

### Tools
- **QGIS:** Desktop GIS for visual exploration and validation
- **Google Earth Engine:** For large-scale satellite data analysis
- **OSMnx:** Python package for OpenStreetMap data

### Books
- "Python for Geospatial Data Analysis" by Bonny P. McClain
- "Spatial Analysis with Python" by Luc Anselin

---

**Last Updated:** December 2025

For questions or suggestions about this guide, please open an issue in the repository.
