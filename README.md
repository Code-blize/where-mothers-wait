# Where Mothers Wait
### Mapping Maternal Health Access Gaps Across Nigeria's 774 Local Government Areas

[![StoryMap](https://img.shields.io/badge/Live%20StoryMap-ArcGIS-blue)](https://arcg.is/1KHqS41)
[![Competition](https://img.shields.io/badge/State%20of%20the%20Map%20US-2026%20Entry-red)](https://state-of-the-map-us-story-map-competition-global-community.hub.arcgis.com/)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)

---

## Overview

**Where Mothers Wait** is a geospatial analysis and narrative map that visualises maternal health facility access gaps across all 774 Local Government Areas (LGAs) in Nigeria.

The project was submitted to the **State of the Map US Narrative Map Competition 2026** under the theme *Open Data for Healthier Ecosystems*, and serves as the data foundation for [MamaAlert](https://github.com/Code-blize) — an AI-powered maternal danger sign triage and facility routing tool for Nigerian mothers.

**[→ View the live StoryMap](https://arcg.is/1KHqS41)**

---

## The Question

Nigeria accounts for roughly one in five of the world's maternal deaths. National-level statistics exist — but they average away the geographic reality of where access breaks down.

This project asks: **at the LGA level — the scale where mothers actually live — where are the gaps?**

---

## Key Findings

- **51,022** health facilities recorded across Nigeria (GRID3 NGA v2.0, Nov 2024)
- **88%** of those facilities are Primary level only — insufficient for obstetric emergencies
- Several LGAs have **zero recorded facilities** in the GRID3 registry
- The **northeast** (Borno, Yobe, Adamawa) shows the most concentrated crisis — conflict has dismantled health infrastructure at the LGA level
- **Proximity to a state capital does not guarantee access** — green at state level hides red at LGA level

---

## Data Sources

All data used in this project is open and freely available. Raw data files are not included in this repository. Download instructions below.

| Dataset | Source | Format | Link |
|---|---|---|---|
| GRID3 NGA Health Facilities v2.0 | GRID3 | CSV | [grid3.org/data](https://grid3.org/data) |
| Nigeria LGA Boundaries (ADM2) | OCHA/HDX | Shapefile (.zip) | [data.humdata.org](https://data.humdata.org/dataset/cod-ab-nga) |
| Nigeria OSM Health Facilities | OpenStreetMap via Overpass Turbo | GeoJSON | [overpass-turbo.eu](https://overpass-turbo.eu) |

### Downloading the OSM data

Go to [overpass-turbo.eu](https://overpass-turbo.eu) and run the following query, then export as GeoJSON:

```
[out:json][timeout:60];
area["name"="Nigeria"]["admin_level"="2"]->.searchArea;
(
  node["amenity"="hospital"](area.searchArea);
  node["amenity"="clinic"](area.searchArea);
  node["amenity"="health_post"](area.searchArea);
  node["healthcare"](area.searchArea);
  way["amenity"="hospital"](area.searchArea);
  way["amenity"="clinic"](area.searchArea);
);
out body;
>;
out skel qt;
```

---

## Repository Structure

```
where-mothers-wait/
│
├── README.md
├── notebooks/
│   └── where_mothers_wait.ipynb     # Full analysis pipeline
├── data/
│   └── README.md                    # Data sources and download instructions
├── outputs/
│   ├── lga_access_map.geojson       # LGA choropleth layer (774 LGAs)
│   ├── critical_lgas.geojson        # Crisis-zone LGAs only
│   ├── grid3_facilities.geojson     # All 51k facility points
│   └── underserved_states.png       # Bar chart — most underserved states
└── docs/
    └── methodology.md               # Full methodology and analytical decisions
```

---

## Reproducing the Analysis

### Requirements

```bash
pip install geopandas pandas folium matplotlib mapclassify
```

Or in Google Colab:

```python
!pip install geopandas folium mapclassify -q
```

### Steps

1. Download the three datasets listed above and place them in the project root
2. Open `notebooks/where_mothers_wait.ipynb`
3. Update the file paths in Step 1 if needed
4. Run all cells — the notebook generates all five output files

**Expected runtime:** ~5 minutes in Google Colab

---

## Methodology Summary

The analysis follows four steps:

**1. Data preparation**
GRID3 facilities are loaded and converted to a GeoDataFrame (point geometry). The LGA shapefile is loaded as polygon geometry. Both use EPSG:4326 (WGS84).

**2. Spatial join**
Each facility point is assigned to the LGA polygon it falls within using `gpd.sjoin()` with `predicate='within'`. This links 51,022 point features to 774 polygon features.

**3. Aggregation and classification**
Facilities are counted per LGA and broken down by level (Primary / Secondary / Tertiary). Each LGA is classified into an access category based on total facility count:

| Category | Facility Count |
|---|---|
| No Facilities | 0 |
| Critical Gap | 1–5 |
| Low Access | 6–20 |
| Moderate Access | 21–50 |
| Higher Access | 50+ |

**4. Export and visualisation**
Three GeoJSON files are exported for ArcGIS StoryMaps. A static bar chart is generated with Matplotlib.

For full methodology detail including limitations, analytical decisions, and what a production-quality extension would include, see [`docs/methodology.md`](docs/methodology.md).

---

## Limitations

This is a first-iteration analysis. Key limitations to be aware of:

- **Count ≠ access** — facility count per LGA does not account for internal geography, road networks, or travel time
- **Functional status unknown** — GRID3 records registered facilities; operational status is not verified
- **No population weighting** — facility counts are not normalised by LGA population
- **Primary facilities cannot manage obstetric emergencies** — a maternal-specific analysis would weight secondary and tertiary facilities more heavily
- **OSM layer not integrated quantitatively** — used for visual reference only in this version

---

## Connection to MamaAlert

This project is the geospatial foundation for **MamaAlert** — an AI-powered maternal health tool being developed for Nigerian mothers. MamaAlert combines:

1. A symptom triage chatbot for maternal danger signs
2. An ML risk prediction layer
3. A geospatial facility routing layer

The LGA-level access analysis in this repository informs the routing layer: knowing which LGAs have facilities — and which don't — is the prerequisite for telling a mother where to go when symptoms appear.

---

## Attribution

```
Map data © OpenStreetMap contributors, available under the Open Database License
openstreetmap.org/copyright

GRID3 NGA Health Facilities v2.0 — grid3.org

Nigeria LGA Boundaries: OCHA Field Information Services Section (FISS)
data.humdata.org
```

---

## Author

**Blessing Obasi-Uzoma**
Geospatial Data Scientist | Physics Graduate, FUTO
[GitHub](https://github.com/Code-blize) · [LinkedIn](https://linkedin.com/in/blessingobasiuzoma) · [Medium](https://medium.com/blessingobasiuzoma)

---

*Submitted to the State of the Map US Narrative Map Competition 2026 · Theme: Open Data for Healthier Ecosystems*
