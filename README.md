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
├── Dashboard/
│   └── NBA_Scouting_Platform.pbix   # Power BI dashboard
|

Data Sources

All data was extracted from the open-source nba_api package using the latest completed NBA regular season (2024-2025) and regular-season-only records.

API endpoints used
Endpoint	Parameter	Description
leaguedashptstats	CatchShoot	Catch-and-shoot statistics
leaguedashptstats	Drives	Driving-related statistics
leaguedashptstats	Passing	Passing-related statistics
leaguedashptstats	Efficiency	Efficiency statistics
leaguedashptstats	SpeedDistance	Movement statistics
leaguedashptstats	Defense	Defensive action statistics
leaguedashptstats	Rebounding	Rebounding statistics
leaguedashplayerstats	default	Main season player statistics
leaguehustlestatsplayer	default	Hustle statistics
leaguedashplayerbiostats	default	Player bio and physical data
leaguedashptdefend	default	Defensive matchup statistics
leaguedashptdefend	3 Pointers	Three-point defensive matchup statistics
shotchartdetail	default	Shot-level event data
Unit of observation

For all datasets except shot chart data, each row represents a player’s aggregated 2024-2025 regular season performance.

The shot chart dataset contains event-level shot observations.

Preprocessing Pipeline
1. Minutes threshold filtering

Players in the lowest quartile of total minutes played were excluded:

Threshold: < 314.58 total minutes
Removed low-sample players, G-League call-ups, and zero-minute records
Final season-level sample size: 426 players
2. Missing values

Only catch-and-shoot three-point variables contained missing values:

CATCH_SHOOT_FG3M
CATCH_SHOOT_FG3A
CATCH_SHOOT_FG3_PCT

Since these missing values corresponded to players with no recorded actions in those categories, they were imputed with 0.

3. Consistency validation

Validation checks included:

Percentage range checks (0 to 1)
Logical consistency checks between made and attempted stats
Duplicate player detection
Cross-dataset consistency checks for:
MIN
GP
W
L

When inconsistencies were found across endpoints, values from the general player stats dataset were used as the baseline.

4. Outlier handling

No outliers were removed. Extreme values were treated as valid basketball performances.

Normalization

All volume-based features were normalized using per-36 minutes scaling:

Per36 = (metric / total_minutes) * 36

This was applied to reduce bias caused by differences in playing time and improve comparability across players.

Feature Engineering

In addition to raw NBA API statistics, multiple advanced metrics were created to improve interpretability and dimensional relevance.

Examples of engineered metrics
Shooting
Free-throw effectiveness
Points per shot
Points %
Field goal attempted %
Three-pointers attempted %
Playmaking
Passer rating
Assist-to-turnover ratio
Turnover %
Passes %
Assist ratio
Points per assist
Defense
Rim % +-
Personal fouls %
Blocks %
Steals %
Defensive Performance Index (DPI)
Rebounding
Box-out effectiveness
Rebound +-
Time variable for shot chart data

A game_time variable was created from period and time-remaining columns to support temporal shot trend analysis.

Final Datasets

The final processed datasets are separated by basketball dimension for downstream clustering and dashboard use.

Dataset	Description	Dimensions
general	Overall player performance	(426, 41)
shoot	Shooting style and scoring profile	(426, 57)
passing	Playmaking and passing profile	(426, 45)
rebound	Rebounding profile	(426, 46)
defense	Defensive and hustle profile	(426, 61)
shot chart	Shot-level event data	(219527, 25)
Exploratory Data Analysis

EDA was used to evaluate the suitability of clustering methods and guide preprocessing decisions.

Main checks included:

Distribution analysis
Skewness and kurtosis
Correlation heatmaps
Variance heterogeneity
PCA projections for shape inspection
Main technical findings
Strong variance heterogeneity across all dimensions
Non-spherical and overlapping cluster structures
Frequent right-skewed distributions
Moderate but non-redundant correlations between features

These findings motivated:

Standardization with StandardScaler
GMM instead of k-means
PCA for the shooting model due to stronger multicollinearity
Modeling Approach
Baseline model

A k-means baseline was tested for comparison.

Why k-means was not selected
Assumes spherical clusters
Assumes equal variance
Produces hard assignments
Did not provide practically useful cluster structures
Silhouette score favored low k values (mostly separating high vs low performers)
Final model: Gaussian Mixture Models

Separate GMMs were trained for each basketball dimension.

Why GMM
Supports probabilistic assignments
Captures hybrid player profiles
Allows different covariance structures
Handles elliptical clusters better than k-means
Parameter estimation

Models were fit using the Expectation-Maximization (EM) algorithm with:

n_init = 10
reg_covar = 1e-5
max_iter = 500
controlled random seeds for reproducibility
Covariance structures evaluated
full
diag
spherical
Selection criteria

Each model was evaluated over cluster sizes 2-10 using:

Bayesian Information Criterion (BIC)
Akaike Information Criterion (AIC)
Calinski-Harabasz Index (CH)
Davies-Bouldin Index (DB)
Average Maximum Posterior Probability

Final model selection combined statistical criteria with interpretability.

Final model configuration
Dimension	Clusters	Covariance
General	8	Diagonal
Shooting	5	Full
Playmaking	4	Diagonal
Rebounding	4	Full
Defense	5	Diagonal
Dimensionality Reduction

PCA was applied only to the shooting feature set before clustering.

PCA settings
Retained 95% explained variance
Used to reduce redundancy caused by high correlations
Clustering was performed on PCA-transformed standardized features
Validation and Robustness
Internal validation metrics

The following metrics were used to compare models:

BIC: penalized likelihood, primary GMM selection criterion
AIC: less strict complexity penalty
Calinski-Harabasz: between/within cluster separation
Davies-Bouldin: cluster overlap/compactness
Average Maximum Probability: confidence of assignments
Robustness checks

To reduce risk of local optima:

Models were rerun with different random seeds
Seed 42 and seed 123 produced similar results
Software Stack
Python 3.12.12
Google Colab
Main libraries
nba_api==1.11.3
pandas==2.2.2
numpy==2.0.2
scikit-learn==1.6.1
scipy==1.16.3
matplotlib==3.10.0
seaborn==0.13.2
Installation

Clone the repository:

git clone https://github.com/emimkuri/NBA_MultiFacet_GMM_Clustering.git
cd NBA_MultiFacet_GMM_Clustering

Install dependencies:

pip install -r Requirements.txt
Running the Project

Open and run:

NBA_Thesis_Clustering.ipynb

The notebook contains the full pipeline:

Data extraction from nba_api
Cleaning and preprocessing
Feature engineering
Normalization and scaling
Exploratory data analysis
PCA for shooting features
GMM model training and evaluation
Cluster assignment generation
Export of final datasets for dashboard usage
Outputs
CSV datasets

Processed datasets stored in /Data:

general.csv
shoot.csv
passing.csv
rebound.csv
defense.csv
sc.csv
Dashboard

Interactive Power BI file:

Dashboard/NBA_Scouting_Platform.pbix
Reproducibility Notes
Season used: 2024-2025 NBA regular season
Filtering threshold: lowest quartile of minutes played
All clustering inputs were standardized before modeling
GMM hyperparameters and selection process are fixed in the notebook
Random seeds were tested for stability
Limitations of the Technical Pipeline
nba_api endpoint documentation is limited
Some endpoint-level inconsistencies required manual harmonization
Per-36 normalization assumes linear scaling
Defensive and rebounding variables remain partially context-dependent
Players with low minutes were excluded to reduce noise
