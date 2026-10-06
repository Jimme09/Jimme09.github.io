# Bhutan Spatio-Temporal Land-Use Explorer

![Bhutan Land-Use Explorer — agriculture land choropleth with charts](../assets/images/bhutan-lulc-dashboard.jpg)

## Overview

A full-stack web GIS dashboard I built to explore land use across Bhutan's 20 Dzongkhags and how it changed between 2016 and 2020. It loads the National Land Commission Secretariat's LULC 2016 and LULC 2020 datasets into a PostGIS database. The app computes area statistics and class-to-class transitions live, and shows them on an interactive map with linked charts. A district can be picked from a dropdown or by clicking it on the map, and an LLM writes a short, data-grounded overview of the selected district.

**Study Area:** National (all 20 Dzongkhags, Bhutan)
**Role:** Solo project — data preparation, database, backend API and frontend
**Status:** Working prototype (runs locally)

---

## Methods & Tools

**Data Sources**

- Land Use Land Cover 2016 and 2020 — National Land Commission Secretariat (NLCS)
- Dzongkhag and Gewog administrative boundaries — NLCS

**Processing Steps**

1. Loaded the LULC and boundary shapefiles into PostgreSQL/PostGIS with `shp2pgsql`, reprojecting from DrukRef03 (EPSG:5266) to WGS84 (EPSG:4326)
2. Overlaid the 2016 and 2020 layers with `ST_Intersection` to build district-level and national land-use transition matrices (area per 2016 → 2020 class pair, in km²)
3. Built a Node.js/Express REST API serving class statistics, transition data, per-class district breakdowns and GeoJSON boundaries, using parameterized SQL queries
4. Built an OpenLayers + Chart.js frontend: a doughnut chart of class areas, a net-change bar chart and a transition table, all linked to a clickable district map
5. Added a hover-driven choropleth. Hovering a class in the chart shades every Dzongkhag by that class's area
6. Served the LULC 2020 layer as WMS through MapServer, using the official class colour scheme
7. Integrated an LLM (Groq API) that summarises each district's land-use profile from its computed statistics

**Tools Used**

| Tool                    | Purpose                                                |
| ----------------------- | ------------------------------------------------------ |
| PostgreSQL / PostGIS    | Spatial database, overlay analysis, area aggregation   |
| Node.js / Express       | REST API between the database and the web client       |
| OpenLayers              | Interactive web map, vector and WMS layers             |
| Chart.js                | Land-use distribution and change charts                |
| MapServer               | WMS rendering of the LULC 2020 layer                   |
| QGIS                    | Data inspection and preparation                        |
| Groq API (LLM)          | Natural-language district summaries                    |

---

## Key Findings

- Forests dominate Bhutan's land cover at roughly 26,700 km² — about 69% of the country in the 2020 data
- About 8,600 km² (≈22% of the country) is mapped under a different class in 2020 than in 2016
- Part of this "change" reflects differences in mapping method and class definitions between the two datasets, not real change on the ground. For example, *Sandy Bank* only exists in the 2020 classification. The transition figures are therefore treated as indicative
- Southern Dzongkhags such as Samtse hold the largest share of agricultural land, which stands out clearly in the agriculture choropleth
