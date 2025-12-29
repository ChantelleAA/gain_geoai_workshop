# Quick Start Guide: Pre-Workshop Setup

**Workshop:** Mapping Rice Affordability in Ghana Using GeoAI  
**Date:** March 2026  
**Duration:** 2 hours



##  Before the Workshop (30 minutes setup time)

Please complete these steps **before** attending the workshop to ensure a smooth experience.



## Step 1: Install Python (10 minutes)

### Check if Python is already installed:
Open a terminal/command prompt and run:
```bash
python --version
```

You need **Python 3.10 or higher**. If you don't have it:

- **Windows/Mac:** Download from [python.org](https://www.python.org/downloads/)
- **Linux:** Use your package manager (e.g., `sudo apt install python3.10`)



## Step 2: Set Up Your Environment (5 minutes)

### Option A: Using a virtual environment (recommended)

```bash
# Navigate to workshop folder
cd path/to/geoai-rice-affordability-ghana

# Create virtual environment
python -m venv venv

# Activate it
# On Mac/Linux:
source venv/bin/activate

# On Windows:
venv\Scripts\activate
```

### Option B: Use your global Python installation
Skip to Step 3 if you prefer not to use a virtual environment.



## Step 3: Install Required Libraries (10 minutes)

With your environment activated, install all dependencies:

```bash
pip install -r requirements.txt
```

This will install:
- pandas, geopandas (data manipulation)
- matplotlib (visualization)
- gradio (interactive interface)
- jupyter (notebook environment)

**Note:** Installation may take several minutes depending on your internet speed.



## Step 4: Download Data Files (5 minutes)

### WFP Food Prices
1. Visit: https://data.humdata.org/dataset/wfp-food-prices-for-ghana
2. Download the CSV file
3. Save as `data/wfp_food_prices_gha.csv`

### Ghana Boundaries
1. Visit: https://www.geoboundaries.org/
2. Search for "Ghana"
3. Download:
   - ADM1 (Regions) as GeoJSON  save as `data/geoBoundaries-GHA-ADM1.geojson`
   - ADM2 (Districts) as GeoJSON  save as `data/geoBoundaries-GHA-ADM2.geojson`

### Your data folder should look like:
```
data/
 wfp_food_prices_gha.csv
 geoBoundaries-GHA-ADM1.geojson
 geoBoundaries-GHA-ADM2.geojson
```



## Step 5: Test Your Setup (5 minutes)

Launch Jupyter and open the workshop notebook:

```bash
jupyter notebook
```

This should open in your browser. Navigate to:
```
notebooks/complete_workshop_notebook.ipynb
```

Run the first few cells to verify everything works. You should see:
- Libraries import successfully
- Data files load without errors



##  Checklist

Before the workshop, make sure you have:

- [ ] Python 3.10+ installed
- [ ] Virtual environment created and activated (optional but recommended)
- [ ] All libraries installed via requirements.txt
- [ ] WFP food price CSV downloaded and in `data/` folder
- [ ] Ghana ADM1 and ADM2 boundary files downloaded and in `data/` folder
- [ ] Jupyter Notebook launched successfully
- [ ] Workshop notebook opens without errors



##  Troubleshooting

### "Module not found" error
- Make sure you activated your virtual environment
- Try reinstalling: `pip install -r requirements.txt`

### "File not found" error
- Check that data files are in the correct `data/` folder
- Verify file names match exactly (case-sensitive)

### Installation takes too long
- Use a faster internet connection if possible
- Some libraries (especially geopandas) have many dependencies

### Still having issues?
- Arrive 10 minutes early to the workshop for technical support
- Or contact the instructor beforehand



##  Optional: Familiarize Yourself

If you have extra time before the workshop:

1. **Read the student handout** (`docs/student_handout.md`)
2. **Review the slides** (`slides/GeoAI_Workshop_Slides.pptx`)
3. **Skim the workshop notebook** to see what we'll cover



##  What to Bring to the Workshop

- Your laptop with everything set up
- Charger (workshop is 2 hours)
- Curiosity and questions!
- Optional: Notebook for additional notes



##  Tips for Success

- **Test your setup the day before** so you have time to fix any issues
- **Close unnecessary programs** during the workshop to free up memory
- **Don't worry if you get stuck**  the instructor will help, and you can always catch up
- **Ask questions**  if you're confused, others probably are too!



**See you at the workshop!**

If you encounter any issues during setup, don't hesitate to reach out to the instructor or arrive early for technical assistance.