# Mobility Maps from Google Timeline Data

**Road usage, place visits, and spatial intensity maps**

This project turns **Google Maps Timeline Data** into high-quality spatial maps showing:

*   Roads you **frequent** vs roads you **never used**
*   Places you spend time at (time-weighted)
*   Visit purpose (POIs: cafés, universities, hospitals, etc.)
*   Continuous spatial intensity fields (KDE / hexbin)
*   Weekday vs weekend mobility patterns

All outputs are publication-ready (high DPI PNGs) and saved to the `outputs/` folder.

---

## 1. Input data: Google Maps Timeline

### How to export your data

Google has moved Location History to on-device "Timeline" storage. To export your data:

1.  Open the **Google Maps** app on your phone.
2.  Tap on your **profile picture**.
3.  Select **Your data in Maps**.
4.  Scroll down and tap on **Download your data**.
5.  Follow the prompts to export your **Timeline** data (JSON format).
6.  Once downloaded, extract the zip file.
7.  Locate the main JSON file containing your history (often named `Records.json` or similar).
8.  Rename or copy it into this project folder as:

    ```text
    location-history.json
    ```

> [!NOTE]
> The script expects a single JSON file. If your export is split by year/month, you may need to merge them or analyze one at a time.

---

## 2. Environment setup

### Python version

*   Python **3.9+** recommended

### Required libraries

Install dependencies:

```bash
pip install numpy pandas matplotlib geopandas shapely osmnx scikit-learn rasterio contextily arabic-reshaper python-bidi
```

> [!TIP]
> OSMnx relies on the public **Overpass API**. Large regions or `network_type="all"` may take time to download.

---

## 3. Project structure

```text
.
├── location-history.json        # Google Maps export (input)
├── vector_map.ipynb             # Road + place vector maps analysis
├── kde_processing.ipynb         # KDE / hexbin intensity maps analysis
├── outputs/                     # Generated figures and tables (auto-created)
├── read_nb.py                   # Utility script
└── README.md
```

---

## 4. Choosing the city / region

### Base city

At the top of the notebooks, configure your target area:

```python
CITY_NAME = "Beirut, Lebanon"
```

Change this to **any city recognized by OpenStreetMap**, e.g.:

*   `"Paris, France"`
*   `"Berlin, Germany"`
*   `"New York City, USA"`

### Expanding beyond city boundaries (recommended)

Google location data often extends beyond administrative borders. Use a **buffer** to include suburbs:

```python
BUFFER_KM = 8   # radius around the city in kilometers
```

*   `4–6 km` → compact city
*   `8–15 km` → city + suburbs
*   `20+ km` → regional mobility

---

## 5. Road network type

```python
NETWORK_TYPE = "all"
```

*   `"drive"` → car-accessible roads only (fast, clean)
*   `"walk"` → pedestrian paths only
*   `"all"` → **everything** (roads + footways + paths)

---

## 6. Key processing parameters

### Visit cleaning

```python
MIN_VISIT_MIN = 3      # ignore micro-stops (< 3 minutes)
MERGE_GAP_MIN = 10     # merge same-place visits if gap ≤ 10 min
```

### Movement weighting

```python
DT_CLIP_MAX_MIN = 30   # max time gap contribution per edge
```

### Speed sanity check

```python
MAX_SPEED_MPS = 60     # max valid speed (~216 km/h)
```

---

## 7. Spatial intensity maps (KDE / hexbin)

### KDE bandwidth

```python
BW = 150   # meters
```

Controls **spatial smoothing**:
*   `50–75 m` → very local (street-level)
*   `100–150 m` → block scale (**recommended**)
*   `250–400 m` → neighborhood scale

### Grid resolution

```python
CELL = 10   # meters per pixel
```

Controls rendering detail. Smaller = higher quality but slower.

---

## 8. Output maps

All generated files are saved to the `outputs/` directory.

### Vector maps
*   **Roads I frequent**: Thicker lines indicate more time spent.
*   **Roads never used**: Binary comparison.
*   **Places I frequent**: Point size proportional to dwell time.
*   **POI categories**: Color-coded by visit purpose.
*   **Weekdays vs weekends**: Comparison maps.

### Raster/intensity maps
*   **KDE intensity**: Point-intensity field over roads.
*   **Hexbin heatmaps**: Visit density by hexagonal bins.
*   **Category-specific density**: Density of visits for specific POI types.

---

## 9. Arabic labels

Included helpers `arabic-reshaper` and `python-bidi` support correct rendering of Arabic text in plots (titles, legends).

---

## 10. Performance notes

It is **normal** for some steps to take time, especially:
*   Large buffers + `network_type="all"` (slow Overpass downloads).
*   POI queries (`amenity/shop/tourism`) returning many geometries.
*   KDE with small `CELL` size over large regions.

---

## 11. Interpretation caveats

*   **"Never visited roads"**: Means no timeline points snapped to them.
*   **Sparse GPS**: Sampling can miss short road segments.
*   **Relative results**: Analysis is relative to the downloaded OSM network.
*   **KDE**: Represents probability/intensity, not exact presence.

---

## Summary

This project provides a **full mobility analysis pipeline** from raw Google Timeline data to network-based road usage, place dwell analysis, and continuous spatial intensity maps. All parameters are explicit, documented, and adjustable.
