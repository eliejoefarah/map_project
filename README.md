# Mobility Analytics Dashboard

**Turn your Google Maps Timeline into data science portfolio pieces.**

This project processes raw location history into:
1.  **Network Analysis**: Road usage classification, frequent routes, and map-matched statistics.
2.  **Spatial Intensity**: Beautiful KDE heatmaps and hexbin density plots.
3.  **Place Analytics**: Semantic categorization of visits and dwell-time distributions.

The notebooks are structured as narrative-driven analyses, ready to run and export high-quality figures to `outputs/`.

---

## 1. Getting Started

### Prerequisites
*   Python 3.8+
*   Google Takeout Data (JSON format)

### Installation
```bash
pip install -r requirements.txt
# OR manually:
pip install numpy pandas matplotlib geopandas shapely osmnx scikit-learn
```

### Data Import
1.  Export **Location History (Timeline)** from the Google Maps app (Settings > Your data in Maps).
2.  Save the JSON file as `location-history.json` in this folder.

---

## 2. The Notebooks

### 🗺️ `vector_map.ipynb`: Network & POI Analysis
**Focus:** Structure and connectivity.
*   **Map Matching**: Snaps noisy GPS points to the OpenStreetMap road network.
*   **Road Usage**: Visualizes which specific street segments you use most.
*   **Place Analytics**: Identifies your top spots and categorizes them (e.g., "Restaurant", "University").
*   **Key Outputs**:
    *   `frequented_roads.png`: Heatmap of road segments.
    *   `places_frequented.png`: Dwell-time weighted locations.
    *   `visit_categories_bar.png`: Chart of top semantic types.
    *   `visits_summary.csv`: Cleaned data of your stops.

### 🔥 `kde_processing.ipynb`: Spatial Density
**Focus:** Continuous fields and intensity.
*   **KDE (Kernel Density Estimation)**: Creates smooth probability fields of your presence.
*   **Hexbin Maps**: Discrete binning for high-contrast density visualization.
*   **Key Outputs**:
    *   `kde_heatmap.png`: Smooth density field.
    *   `hexbin_map.png`: Aggregated point density.

---

## 3. Configuration & Tuning

Adjust these variables at the top of the notebooks:

*   **`CITY_NAME`**: "Beirut, Lebanon", "Paris, France", etc.
*   **`BUFFER_KM`**: Radius around the city center to include (default 8km).
*   **`MIN_VISIT_MIN`**: Minimum duration to count as a stop (default 3 mins).

---

## 4. Outputs

All generated files are saved to the `outputs/` directory. The notebooks are designed to be idempotent—re-running them overwrites the previous outputs.
