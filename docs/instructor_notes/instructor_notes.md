# Instructor Notes: Mapping Rice Affordability in Ghana Using GeoAI

**Event:** GAIN Monthly Dialogue Session  March 2026  
**Created by:** Chantelle Amoako-Atta, AI/ML Engineer and PhD Researcher (Decarb-AI, UCD)  
**Duration:** 2 hours  
**Audience:** Technical participants who are comfortable with Python
**Recommended notebook:** Use `notebooks/01_rice_affordability_geoai_notebook.ipynb` for delivering the session.

**Folder conventions:** Base data in `data/raw/`, processed outputs in `data/processed/`, and notebook artifacts in `notebooks/output/`.



## High-Level Structure

- **010 min:** Introduction, context, goals, WFP background
- **1030 min:** Data loading and understanding
- **3055 min:** GeoDataFrame and regional analysis
- **5580 min:** District analysis and data gaps
- **80100 min:** LLM and Gradio interface demo
- **100120 min:** Discussion, wrap-up, extensions



## Detailed Teaching Plan

### 010 min: Introduction and Context

**Opening message:**  
"Welcome! Today we will explore rice affordability in Ghana using real market data and GeoAI tools. By the end, you will have created maps showing spatial price patterns and built a natural language interface to query your analysis."

**Cover:**
- Why rice prices matter in Ghana (staple food, affordability impacts daily life)
- What GeoAI means: artificial intelligence combined with geographic data to solve location-specific problems
- Learning outcomes and roadmap for the session

**Introduce WFP and HDX:**
- The World Food Programme (WFP) monitors food prices in markets across Ghana and other countries
- This data supports humanitarian response, policy decisions, and food security planning
- Data is publicly accessible through the Humanitarian Data Exchange (HDX)
- Emphasize that this is real-world data used by decision-makers

**Key messaging:**  
"We will turn real Ghanaian market data into maps and insights, then interact with them using an LLM. You will see how Python handles the numeric work while the LLM helps communicate results."



### 1030 min: Data Understanding

**Activities:**
- Load WFP food price dataset and geoBoundaries administrative boundaries
- Inspect first rows and column meanings
- Filter for rice observations
- Validate that price and coordinates are numeric

**Engagement questions:**
- "What does each row in the price dataset represent?"
- "Why might we filter specifically for rice?"
- "Where are these market observations located?"

**Teaching tips:**
- Normalize column names early (lowercase, replace spaces)
- Encourage questions about what columns mean
- Highlight that missing data is normal in real-world datasets
- Point out that some markets have coordinates and some do not

**Common issues:**
- Non-numeric price values: use `pd.to_numeric()` with `errors='coerce'`
- Missing coordinates: drop rows where latitude or longitude is null



### 3055 min: Regional Analysis (ADM1)

**Activities:**
- Convert price table to GeoDataFrame using point geometry
- Plot Ghana's regions for spatial orientation
- Perform spatial join between rice observations and ADM1 regions
- Aggregate by region using median
- Create choropleth map showing regional rice prices

**Engagement questions:**
- "Why use median instead of mean?"
- "Which regions show higher rice prices? What might explain this?"
- "How does spatial join work conceptually?"

**Key concepts to emphasize:**
- **Median vs mean:** Median is resistant to outliers and more stable for market prices
- **Spatial join:** Connecting each price observation to the region it falls within
- **Choropleth map:** Areas colored by a data value (here, median price)

**Expected insights:**
- Greater Accra may show higher prices (urban demand, imports)
- Northern regions might show lower or higher prices depending on production and transport
- Coastal regions may differ from inland regions

**Teaching tips:**
- Use the map to tell a story about Ghana's geography
- Ask participants to hypothesize before revealing the map
- Discuss production zones, transport costs, and demand patterns

**Common issues:**
- Blank maps: check that CRS is set correctly (`EPSG:4326` for WGS84)
- No spatial join results: verify geometry is valid



### 5580 min: District Analysis (ADM2) and Data Gaps

**Activities:**
- Perform spatial join between rice observations and ADM2 districts
- Apply threshold (only include districts with >= 2 observations)
- Create district-level choropleth
- Visualize districts with data (colored) versus districts without data (outlined only)

**Engagement questions:**
- "What do you notice about the district-level map compared to regions?"
- "Where do we have data? Where is it missing?"
- "What might explain these gaps?"

**Key concepts to emphasize:**
- **Data gaps reflect monitoring coverage, not absence of markets or issues**
- WFP monitors specific markets; not all districts are covered
- Missing data can affect planning and resource allocation
- Thresholding helps ensure robust estimates (single observations may not be representative)

**Expected observations:**
- Many districts have no rice price observations
- Some districts have sparse data
- Coverage may be concentrated in urban centers or accessible areas

**Discussion points:**
- Why might certain districts lack data? (Remote, low market activity, resource constraints)
- How should policymakers interpret missing data?
- What would be needed to fill these gaps?

**Teaching tips:**
- Use this as an opportunity to discuss real-world data limitations
- Emphasize critical thinking about what data shows and what it doesn't
- Connect to participants' own experiences with data collection challenges

**Common issues:**
- No districts appear on map: use `predicate='intersects'` instead of `predicate='within'`
- Too few districts: lower threshold or check coordinate quality



### 80100 min: LLM Interface with Gradio

**Activities:**
- Introduce the concept: LLM as a natural language interface to your analysis
- Show the code structure:
  - Python function computes numeric results from data
  - LLM generates professional narrative explanation
- Launch Gradio interface
- Demonstrate with example queries

**Example queries to try:**
- "Where is rice cheapest in Ghana?"
- "Which district has the most expensive rice?"
- "Which region has the lowest median rice price?"

**Key concepts to emphasize:**
- **Separation of concerns:** Python handles computation and ensures accuracy; LLM handles explanation and natural language generation
- **No hallucination of values:** The LLM never invents numeric results
- **Practical utility:** Makes analysis accessible to non-technical users

**Engagement:**
- Ask participants to suggest questions
- Let them test the interface
- Discuss what the LLM does well and what it doesn't do

**Teaching tips:**
- Clarify that the LLM is not running the analysis, just interpreting it
- Emphasize that this pattern can be applied to many domains
- Mention that the approach is extensible (could add more commodities, time filters, etc.)

**Common issues:**
- Gradio fails to launch: check port availability, restart kernel if needed
- API errors: have a fallback to deterministic Python-only answers
- Slow responses: normal for LLM calls; set expectations



### 100120 min: Wrap-Up and Discussion

**Reflection questions:**
- "What did we learn about rice affordability in Ghana?"
- "How can GeoAI support food security decision-making?"
- "What other questions could we answer with this data?"
- "What would make this more useful in practice?"

**Ideas for extension:**
- Compare local versus imported rice
- Add time trends (animate maps over months or years)
- Include other commodities (maize, gari, tomatoes)
- Combine with population or poverty data to identify vulnerable areas
- Deploy Gradio app for public use or policymakers

**Closing message:**  
"This workshop is a starting point. The tools and approaches you have learned can be applied to many other spatial and data-driven questions. Your ideas and questions will take this further."

**Encourage participants to:**
- Share feedback
- Explore the GitHub repository
- Try extending the analysis on their own



## Expected Student Challenges and Solutions

| Challenge | Solution |
|--|-|
| Confusion about spatial join | Use visual analogy: "dropping pins on a map, then asking which region each pin lands in" |
| Concern about missing values | Normalize this as expected in real-world data; discuss implications |
| Overthinking LLM role | Clarify repeatedly: "LLM writes explanations, Python does the math" |
| Map appears blank | Check CRS, ensure geometry is valid, verify data was joined |
| Difficulty interpreting price patterns | Guide with questions: "What do you know about this region? How might geography affect prices?" |



## Stretch Tasks for Advanced Participants

1. **Compare local vs imported rice:**
   - Filter for "Rice (local)" and "Rice (imported)"
   - Create side-by-side maps
   - Discuss price differences and policy implications

2. **Add time filtering:**
   - Allow users to select a date range
   - Show how prices change over time
   - Identify seasonal patterns

3. **Deploy the Gradio app:**
   - Share publicly or within organization
   - Add authentication if needed
   - Consider hosting options (Hugging Face Spaces, cloud platforms)

4. **Include population data:**
   - Join with census data
   - Calculate affordability indices (price relative to income)
   - Identify high-risk areas

5. **Build a dashboard:**
   - Use Streamlit or Dash
   - Add interactive filters
   - Include multiple commodities



## Additional WFP Datasets to Mention

If participants are interested in further exploration, mention that WFP provides additional datasets on HDX:

- Market functionality indicators
- Prices for other commodities (maize, beans, oil, etc.)
- Time series data for trend analysis
- Regional datasets for other African countries

Encourage participants to explore HDX and consider how these datasets could be combined or compared.



## Technical Requirements

**Software:**
- Python 3.10+
- Libraries: pandas, geopandas, matplotlib, folium or plotly, gradio
- Jupyter Notebook or JupyterLab

**Data files:**
- WFP Ghana food prices CSV (under `data/raw/`)
- GeoBoundaries Ghana ADM1 and ADM2 GeoJSON (under `data/raw/`)

**Optional:**
- API access for LLM (if using external service)
- Internet connection for Gradio sharing



## Session Logistics

**Before the session:**
- Test all code and ensure data files are accessible
- Verify Gradio launches successfully
- Prepare fallback materials in case of technical issues
- Have the GitHub repository URL ready to share

**During the session:**
- Monitor chat for questions
- Adjust pacing based on participant engagement
- Be flexible with timing (some sections may take longer)
- Encourage active participation and discussion

**After the session:**
- Share GitHub repository link
- Provide contact information for follow-up questions
- Collect feedback for future improvements



## Contact and Resources

**Instructor:** Chantelle Amoako-Atta  
**GitHub Repository:** [Will be shared during session]  
**Further reading:**
- GeoPandas documentation
- WFP food price data on HDX
- GeoBoundaries administrative boundary data
- Introductory resources on GeoAI and spatial analysis



**End of Instructor Notes**
