# Student Handout: Mapping Rice Affordability in Ghana Using GeoAI
## Workshop Materials

- Workshop notebook: `notebooks/01_rice_affordability_geoai_notebook.ipynb`
- Data layout: `data/raw/` for source files, `data/processed/` for derived outputs
- Notebook outputs: `notebooks/output/`


**Event:** GAIN Monthly Dialogue Session  March 2026  
**Created by:** Chantelle Amoako-Atta, AI/ML Engineer and PhD Researcher (Decarb-AI, UCD)



## Why This Workshop?

Rice is a staple food in Ghana. Prices vary from market to market due to factors such as production, transportation, and demand. Mapping these differences helps us understand affordability patterns and supports food security decision-making.

We use real data collected by the World Food Programme (WFP), who monitor markets to detect changes that may threaten livelihoods and food access.



## What You Will Learn

By the end of this workshop, you will be able to:

1. Understand what GeoAI means in a practical Ghanaian context
2. Load and explore real-world food price datasets
3. Convert tabular market locations into geospatial data
4. Spatially join points to Ghana's administrative boundaries
5. Create choropleth maps of rice prices across regions and districts
6. Identify and interpret data gaps
7. Use a Large Language Model (LLM) as a natural language interface to your analysis



## Key Concepts

### GeoAI
Using artificial intelligence and geographic data together to answer questions linked to specific places. Example: "Where is rice most affordable in Ghana?"

### GeoDataFrame
A table where each row also has a location stored as geometry (points, lines, or polygons). It can be plotted as a map.

### Spatial Join
Connecting each price observation to the region or district it belongs to based on location. Think of it like dropping pins on a map and asking which area each pin lands in.

### Choropleth Map
A map where areas (regions or districts) are colored based on a data value. In this workshop, we color areas by median rice price.

### Median vs Mean
- **Mean:** Average of all values (sensitive to outliers)
- **Median:** Middle value when data is sorted (resistant to outliers)

For market prices, median is often more stable and representative.

### World Food Programme (WFP)
An international organization that monitors food prices in markets to track food security conditions. Their data is used by governments, NGOs, and humanitarian organizations.



## Workshop Roadmap

1. **Introduction:** Context and goals
2. **Data Loading:** Explore WFP rice price data and Ghana boundaries
3. **Filtering and Cleaning:** Prepare data for analysis
4. **Regional Analysis (ADM1):** Map rice prices across Ghana's regions
5. **District Analysis (ADM2):** Examine district-level patterns and data gaps
6. **LLM Interface:** Ask questions in natural language
7. **Discussion:** Insights, applications, and extensions



## Guided Questions

### Before Coding
**Where do you expect rice to be most affordable in Ghana? Why?**

_Your answer:_

_______________________________________________________________

_______________________________________________________________

**What factors might cause rice prices to vary spatially?**

_Your answer:_

_______________________________________________________________

_______________________________________________________________



### After the Regional Map
**Name two regions with relatively low median rice prices. What might explain this?**

_Your answer:_

_______________________________________________________________

_______________________________________________________________

**Name two regions with higher rice prices. What might explain this?**

_Your answer:_

_______________________________________________________________

_______________________________________________________________

**How do you think transportation and infrastructure affect these patterns?**

_Your answer:_

_______________________________________________________________

_______________________________________________________________



### After the District Map
**Which areas show data gaps (no rice price observations)?**

_Your answer:_

_______________________________________________________________

_______________________________________________________________

**What might explain these missing data patterns?**

_Your answer:_

_______________________________________________________________

_______________________________________________________________

**How might data gaps affect food security planning and policy?**

_Your answer:_

_______________________________________________________________

_______________________________________________________________



### After the LLM Demo
**How is the LLM being used in this analysis? What does it do?**

_Your answer:_

_______________________________________________________________

_______________________________________________________________

**What does the LLM NOT do in this analysis?**

_Your answer:_

_______________________________________________________________

_______________________________________________________________

**Why is it important to separate numeric computation (Python) from explanation (LLM)?**

_Your answer:_

_______________________________________________________________

_______________________________________________________________



## Notes

Use this space for additional observations, questions, or ideas during the workshop.

_______________________________________________________________

_______________________________________________________________

_______________________________________________________________

_______________________________________________________________

_______________________________________________________________

_______________________________________________________________

_______________________________________________________________

_______________________________________________________________

_______________________________________________________________

_______________________________________________________________

_______________________________________________________________

_______________________________________________________________

_______________________________________________________________

_______________________________________________________________

_______________________________________________________________

_______________________________________________________________



## What Else Could We Map?

This workshop focuses on rice, but the same approach can be applied to:

- **Other commodities:** Maize, cassava, tomatoes, gari, beans, cooking oil
- **Seasonal changes:** How prices vary over time
- **Infrastructure:** Roads, market access, transport networks
- **Population and livelihoods:** Combining price data with demographic information
- **Comparative analysis:** Local vs imported varieties



## Where to Go Next

### Extend the Analysis
- Add time trends (animate maps over months or years)
- Compare local versus imported rice prices
- Include other commodities in your analysis
- Combine price data with population or poverty data

### Deploy Your Work
- Share your Gradio app publicly
- Build a dashboard with Streamlit or Dash
- Collaborate with organizations working on food security

### Learn More
- Explore additional WFP datasets on the Humanitarian Data Exchange (HDX)
- Learn more about GeoPandas and spatial analysis
- Investigate GeoAI applications in climate and development



## Additional Resources

**Data sources:**
- Humanitarian Data Exchange (HDX): https://data.humdata.org/
- GeoBoundaries: https://www.geoboundaries.org/
- WFP Food Price Monitoring: https://www.wfp.org/

**Learning resources:**
- GeoPandas documentation: https://geopandas.org/
- Introduction to spatial analysis with Python
- GeoAI and machine learning for spatial data

**Workshop materials:**
- GitHub repository: [Link will be shared]
- Contact: Chantelle Amoako-Atta



## Reflection

### What did you learn?

_______________________________________________________________

_______________________________________________________________

_______________________________________________________________

### What surprised you?

_______________________________________________________________

_______________________________________________________________

_______________________________________________________________

### How might you apply these skills?

_______________________________________________________________

_______________________________________________________________

_______________________________________________________________

### What questions do you still have?

_______________________________________________________________

_______________________________________________________________

_______________________________________________________________



**This workshop is a starting point. Your ideas and questions can take it further.**



**End of Student Handout**
