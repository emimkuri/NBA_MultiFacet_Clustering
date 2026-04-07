# NBA_MultiFacet_GMM_Clustering

Technical repository for extracting, preprocessing, modeling, and visualizing NBA player archetypes using multi-faceted Gaussian Mixture Models (GMMs) across the 2024-2025 NBA regular season.

## Project Overview

This project builds a player classification framework using dimension-specific clustering models instead of a single global player representation. The pipeline extracts season-level NBA data from multiple `nba_api` endpoints, preprocesses and engineers features, normalizes performance by playing time, runs exploratory data analysis (EDA), and trains separate GMM clustering models for five basketball dimensions:

- General performance
- Shooting
- Playmaking
- Rebounding
- Defense

The final outputs are exported for use in an interactive Power BI scouting dashboard.

## Repository Structure

```text
NBA_MultiFacet_GMM_Clustering/
|
├── NBA_Thesis_Clustering.ipynb      # Main notebook: extraction, preprocessing, EDA, clustering
├── README.md                        # Project documentation
├── Requirements.txt                 # Python dependencies
|
├── Data/
│   ├── general.csv                  # Final general dataset
│   ├── shoot.csv                    # Final shooting dataset
│   ├── passing.csv                  # Final playmaking dataset
│   ├── rebound.csv                  # Final rebounding dataset
│   ├── defense.csv                  # Final defensive dataset
│   └── sc.csv                       # Shot chart events dataset
|
├── Dashboard/
│   └── NBA_Scouting_Platform.pbix   # Power BI dashboard
|
└── (optional generated outputs)
