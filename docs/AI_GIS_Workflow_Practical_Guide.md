# 🚀 AI_GIS_Workflow — Complete Practical Guide

**Status:** Repository & Dev Branch Ready ✅

---

## 📋 Table of Contents

1. [Project Quick Overview](#project-quick-overview)
2. [Current Status](#current-status)
3. [Step-by-Step Workflow](#step-by-step-workflow)
4. [ArcGIS Data Processing](#arcgis-data-processing)
5. [Coding Phase](#coding-phase)
6. [Time Management](#time-management)
7. [Quality Checklist](#quality-checklist)

---

## 🎯 Project Quick Overview

**What You're Building:**
A system that understands natural language commands and performs GIS operations automatically.

**Example:**
```
User: "Show hospitals within 500m of rivers"
System: 
  1. Understands the command
  2. Loads hospital data
  3. Loads river data
  4. Creates 500m buffer around rivers
  5. Finds intersection
  6. Displays result on map
```

**Tech Stack:**
- Data: ArcGIS for processing
- Code: Python for automation
- Interface: Streamlit for web app
- NLP: spaCy for language understanding

---

## 📊 Current Status

✅ **DONE:**
- GitHub repository created
- Folder structure ready
- Dev branch created
- Hanane is collaborator

⏳ **NEXT:**
- Download Rabat OSM data
- Process data in ArcGIS
- Upload cleaned data to GitHub
- Build automation code

---

## 🔄 Step-by-Step Workflow

### Phase 1: Data Preparation (Week 1-2)

**STEP 1: Download OSM Data**

**Two Options:**

**Option A: OpenStreetMap Export (5 minutes)**
- Go: https://www.openstreetmap.org/export
- Find Rabat on map
- Draw box around Rabat city
- Click "Export" → Download as .osm file
- Save as: `rabat_raw.osm`

**Option B: Overpass Turbo (10 minutes, more precise)**
- Go: https://overpass-turbo.eu/
- Copy-paste this query:

```
[bbox:33.97,-6.87,34.04,-6.80];
(
  node["amenity"="hospital"];
  way["waterway"="river"];
  way["highway"];
  way["building"];
  node["name"];
);
out center;
```

- Click "Run"
- Export as GeoJSON
- Save as: `rabat_data.geojson`

**Where to Save:** Download to your computer first

---

**STEP 2: Upload Raw Data to GitHub**

1. Go to: https://github.com/laibazarar0707-netizen/AI_GIS_Workflow
2. Make sure you're on **dev** branch
3. Click **"data"** folder
4. Click **"raw"** folder
5. Click **"Add file"** → **"Upload files"**
6. Drag your downloaded file
7. Commit message: `Add raw Rabat OSM data`
8. Click **"Commit changes"**

**Done!** Data is backed up.

---

### Phase 2: Data Processing in ArcGIS (Week 2-3)

**STEP 3: Open Data in ArcGIS**

**In ArcGIS Desktop:**
1. Open ArcMap or ArcGIS Pro
2. File → Add Data
3. Navigate to your downloaded file
4. Add to map

**Or In ArcGIS Online:**
1. Sign in to arcgis.com
2. Click "Create" → "Map"
3. Click "Add" → "Add Layer from File"
4. Upload your file

---

**STEP 4: Process Each Layer**

**For Each Feature Type (Hospitals, Rivers, Roads, Buildings):**

**A. Check Coordinate System**
- Right-click layer → Properties
- Note the CRS/Projection
- Should be EPSG:4326 (WGS84) for Rabat
- If different, use Tools → Project

**B. Remove Unnecessary Columns**
- Right-click layer → Open Attribute Table
- Delete columns you don't need
- Keep: geometry, name, type, ID

**C. Remove Bad Data**
- Look for missing geometries
- Delete rows with NULL values
- Check for duplicates

**D. Clip to Rabat Boundary**
- Draw a rectangle around just Rabat city
- Geoprocessing → Clip
- Select your boundary
- Save clipped version

**E. Export Cleaned Data**
- Right-click layer → Export
- Format: **Shapefile** or **GeoJSON**
- Name: `hospitals_cleaned.shp` (example)
- Save to: `data/cleaned/` folder on computer

---

**STEP 5: Upload Cleaned Data to GitHub**

Same as Step 2:
1. Go to `data/cleaned/` folder on GitHub
2. Upload your cleaned files
3. Commit: `Add cleaned [feature] data`

**Files to Create:**
- `hospitals_cleaned.shp` (or .geojson)
- `rivers_cleaned.shp`
- `roads_cleaned.shp`
- `buildings_cleaned.shp`
- `rabat_boundary.shp` (optional)

---

### Phase 3: Coding Phase (Week 3-4)

**STEP 6: Load Data with Python**

**Create file:** `app/load_data.py`

```python
import geopandas as gpd

# Load cleaned data
hospitals = gpd.read_file('data/cleaned/hospitals_cleaned.shp')
rivers = gpd.read_file('data/cleaned/rivers_cleaned.shp')
roads = gpd.read_file('data/cleaned/roads_cleaned.shp')

print(hospitals.head())
```

---

**STEP 7: Build Layer Discovery System**

**Create file:** `app/layer_discovery.py`

```python
# Dictionary of available layers
LAYERS = {
    'hospitals': 'data/cleaned/hospitals_cleaned.shp',
    'rivers': 'data/cleaned/rivers_cleaned.shp',
    'roads': 'data/cleaned/roads_cleaned.shp',
    'buildings': 'data/cleaned/buildings_cleaned.shp',
}

def find_layer(keyword):
    """Find layer by keyword"""
    keyword = keyword.lower()
    
    if 'hospital' in keyword or 'clinic' in keyword:
        return LAYERS['hospitals']
    elif 'river' in keyword or 'water' in keyword:
        return LAYERS['rivers']
    elif 'road' in keyword or 'street' in keyword:
        return LAYERS['roads']
    elif 'building' in keyword:
        return LAYERS['buildings']
    else:
        return None

# Test
print(find_layer('Show hospitals'))  # Returns hospitals file
```

---

**STEP 8: Build GIS Operations**

**Create file:** `app/gis_operations.py`

```python
import geopandas as gpd

def buffer_layer(layer_path, distance_meters):
    """Create buffer around features"""
    gdf = gpd.read_file(layer_path)
    buffered = gdf.geometry.buffer(distance_meters)
    return buffered

def intersect_layers(layer1_path, layer2_path):
    """Find intersection of two layers"""
    gdf1 = gpd.read_file(layer1_path)
    gdf2 = gpd.read_file(layer2_path)
    intersection = gpd.overlay(gdf1, gdf2, how='intersection')
    return intersection

def clip_to_boundary(layer_path, boundary_path):
    """Clip layer to boundary"""
    gdf = gpd.read_file(layer_path)
    boundary = gpd.read_file(boundary_path)
    clipped = gpd.clip(gdf, boundary)
    return clipped

# Test
hospitals_buffered = buffer_layer('data/cleaned/hospitals_cleaned.shp', 500)
print(f"Buffered hospitals: {len(hospitals_buffered)} features")
```

---

**STEP 9: Connect NLP to GIS**

**Create file:** `app/nlp_parser.py`

```python
import spacy
from layer_discovery import find_layer
from gis_operations import buffer_layer, intersect_layers

nlp = spacy.load('en_core_web_sm')

def parse_command(command):
    """Parse natural language command"""
    doc = nlp(command)
    
    # Extract key information
    operations = []
    layers = []
    distance = None
    
    # Simple parsing logic
    command_lower = command.lower()
    
    if 'buffer' in command_lower or 'around' in command_lower:
        operations.append('buffer')
        # Extract distance
        import re
        match = re.search(r'(\d+)\s*m', command_lower)
        if match:
            distance = int(match.group(1))
    
    if 'hospital' in command_lower:
        layers.append('hospitals')
    if 'river' in command_lower:
        layers.append('rivers')
    if 'road' in command_lower:
        layers.append('roads')
    
    return {'operations': operations, 'layers': layers, 'distance': distance}

def execute_command(command):
    """Parse and execute command"""
    parsed = parse_command(command)
    
    if 'buffer' in parsed['operations'] and parsed['layers']:
        layer_path = find_layer(parsed['layers'][0])
        result = buffer_layer(layer_path, parsed['distance'])
        return result
    
    return None

# Test
result = execute_command('Buffer hospitals by 500m')
print(result)
```

---

**STEP 10: Create Web Interface**

**Create file:** `app/main.py` (Streamlit app)

```python
import streamlit as st
import geopandas as gpd
from nlp_parser import execute_command

st.title('🗺️ AI-Assisted GIS Workflow')
st.write('Enter a command in natural language to perform GIS operations')

# User input
command = st.text_input('Command:', placeholder='E.g., "Show hospitals within 500m of rivers"')

if command:
    st.write(f'**Your command:** {command}')
    
    try:
        result = execute_command(command)
        
        if result is not None:
            st.success('✅ Command executed successfully!')
            
            # Display result
            st.write(result)
            
            # Show on map (optional)
            if 'geometry' in result.columns:
                st.map(result)
        else:
            st.error('❌ Command not recognized. Try again.')
    
    except Exception as e:
        st.error(f'Error: {str(e)}')

st.sidebar.info('ℹ️ Try commands like:\n- "Show hospitals"\n- "Buffer rivers 500m"\n- "Find intersections"')
```

---

**STEP 11: Run the App**

In terminal:
```bash
streamlit run app/main.py
```

Then open: http://localhost:8501

---

### Phase 4: Testing & Deployment (Week 4)

**STEP 12: Test Everything**

Checklist:
- [ ] Data loads correctly
- [ ] All layers are available
- [ ] Buffer operation works
- [ ] Intersection operation works
- [ ] Web interface opens
- [ ] Commands are recognized
- [ ] Results display correctly

**STEP 13: Document Everything**

Create file: `docs/USER_GUIDE.md`

```markdown
# How to Use AI_GIS_Workflow

## Installation

1. Clone repository
2. Install dependencies: `pip install geopandas streamlit spacy folium`
3. Download NLP model: `python -m spacy download en_core_web_sm`

## Running

```bash
streamlit run app/main.py
```

## Example Commands

- "Show hospitals"
- "Buffer rivers 500 meters"
- "Find hospitals near roads"
```

---

## 🏗️ ArcGIS Data Processing Details

### In ArcGIS Pro:

**Workflow:**
1. Import OSM data
2. For each layer:
   - Check/fix CRS
   - Remove unnecessary fields
   - Delete invalid geometries
   - Clip to study area
   - Export as Shapefile

**Key Tools:**
- Geoprocessing → Project (fix CRS)
- Geoprocessing → Clip (limit to Rabat)
- Geoprocessing → Buffer (create zones)
- Geoprocessing → Spatial Join (connect data)

**Output:** Clean shapefiles ready for Python

---

## 💻 Installing Required Software

**ArcGIS Pro:**
- Download from Esri website
- Need license (student/educational available)

**Python (for coding part):**
```bash
pip install geopandas pandas shapely folium streamlit spacy
python -m spacy download en_core_web_sm
```

**Optional:**
```bash
pip install jupyter  # For notebooks
pip install matplotlib  # For plotting
```

---

## 📁 File Organization on GitHub

After all steps, your repository should look like:

```
AI_GIS_Workflow/
├── app/
│   ├── main.py                 # Streamlit web app
│   ├── nlp_parser.py          # Natural language processing
│   ├── gis_operations.py      # Spatial operations
│   ├── layer_discovery.py     # Load correct layers
│   └── load_data.py           # Load data files
├── data/
│   ├── raw/
│   │   ├── rabat_raw.osm
│   │   └── rabat_data.geojson
│   └── cleaned/
│       ├── hospitals_cleaned.shp
│       ├── rivers_cleaned.shp
│       ├── roads_cleaned.shp
│       └── buildings_cleaned.shp
├── notebooks/
│   ├── 01_data_exploration.ipynb
│   └── 02_testing.ipynb
├── docs/
│   ├── USER_GUIDE.md
│   ├── SETUP.md
│   └── TROUBLESHOOTING.md
└── README.md
```

---

## ✅ Commit Messages (Good Practice)

When uploading to GitHub, use clear messages:

```
Add raw Rabat OSM data
Clean hospitals layer
Add layer discovery module
Create NLP parser
Deploy Streamlit interface
Fix buffer operation bug
```

---

## 🔄 GitHub Workflow Summary

1. **Work on dev branch** (never on main)
2. **Download/process data** → Upload to GitHub
3. **Write code** → Upload to GitHub
4. **Test thoroughly** → Make sure it works
5. **Write documentation** → Explain how to use
6. **Merge to main** when complete (optional for final version)

**Commit frequently!** Every small change.

---

## 🚨 Common Issues & Solutions

**Problem:** Data won't load in Python
**Solution:** Check file path, ensure file format is correct (.shp or .geojson)

**Problem:** Command not recognized
**Solution:** Add more keywords to nlp_parser.py

**Problem:** Buffer operation too slow
**Solution:** Use smaller data or lower precision

**Problem:** Streamlit app crashes
**Solution:** Check for missing files or dependencies

---

## 📚 Useful Resources

**ArcGIS:**
- ArcGIS Pro Help: https://pro.arcgis.com/en/pro-app/latest/help/
- GeoPandas docs: https://geopandas.org/

**Python:**
- GeoPandas spatial operations: https://geopandas.org/docs/reference/
- spaCy NLP tutorial: https://spacy.io/usage

**Streamlit:**
- Streamlit documentation: https://docs.streamlit.io/

**GIS Data:**
- OpenStreetMap: https://www.openstreetmap.org/
- Overpass Turbo: https://overpass-turbo.eu/

---

**Last Updated:** February 2025  
**Status:** Ready to Start Data Processing ✅
