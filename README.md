# Earthquake Aftershock Analysis Dashboard

A Streamlit-based data analytics dashboard for exploring earthquake aftershock behavior using seismic event data from the **USGS**. The project processes raw earthquake records, identifies aftershock episodes based on spatial and temporal proximity, computes risk scores, applies clustering and anomaly detection, and provides interactive visualizations along with machine learning-based predictions.

---

## Project Overview

This project analyzes earthquake sequences to identify and study **aftershock episodes**. It transforms raw seismic records into episode-level insights and presents them through an interactive dashboard for exploration, filtering, visualization, and predictive analysis.

The system performs the following tasks:

* Processes raw USGS earthquake data
* Detects mainshock–aftershock episodes using rule-based spatial and temporal thresholds
* Generates episode-level statistics and engineered features
* Applies clustering and anomaly detection to group similar seismic activity patterns
* Calculates composite risk scores for each episode
* Trains machine learning models for aftershock-related prediction tasks
* Displays results through an interactive Streamlit dashboard

---

## Key Highlights

* Processed **17,890 earthquake records**
* Identified **1,887 aftershock episodes**
* Grouped episodes into **7 spatial clusters**
* Classified episodes into **Low, Moderate, and High** activity/risk tiers
* Built **6 machine learning models** for earthquake episode prediction
* Developed a **multi-tab interactive dashboard** with filtering, visualization, and CSV export support

---

## Dataset Summary

| Metric                              | Value  |
| ----------------------------------- | ------ |
| Total earthquake events processed   | 17,890 |
| Aftershock episodes identified      | 1,887  |
| Events in processed episode dataset | 64,872 |
| Spatial clusters                    | 7      |
| Low-risk episodes                   | 1,874  |
| Moderate-risk episodes              | 11     |
| High-risk episodes                  | 2      |

> **Note:** All timestamps are processed in **UTC**, and geographic distances are calculated using the **Haversine formula**.

---

## Features

### 1. Episode Detection

* Identifies mainshock events using magnitude thresholds
* Groups nearby seismic events into aftershock episodes using distance and time windows
* Supports dynamic radius/time rules based on earthquake magnitude

### 2. Risk Analysis

* Computes episode-level risk/activity scores using engineered seismic features
* Categorizes episodes into **Low**, **Moderate**, and **High Activity** groups
* Highlights anomalous and dense aftershock sequences

### 3. Clustering & Anomaly Detection

* Uses **DBSCAN** to identify spatial/behavioral clusters of episodes
* Uses **Isolation Forest** to detect unusual earthquake patterns or episodes

### 4. Interactive Dashboard

* Built with **Streamlit** and **Plotly**
* Provides multi-tab analysis for overview, risk, insights, visualizations, predictions, and raw data exploration
* Supports filtering by episode, risk level, and cluster
* Allows export of filtered results as CSV

### 5. Predictive Modeling

Includes six trained machine learning models for episode-level prediction:

* **Aftershock probability (24h)** – binary classification
* **Next event magnitude** – regression
* **Time to next event** – regression
* **Risk escalation level** – classification
* **7-day event count** – regression
* **Spatial cluster prediction** – classification

---

## Dashboard Sections

The dashboard contains the following analysis sections:

### Overview

* Global summary metrics
* Map of earthquake locations
* Snapshot of processed seismic activity

### Risk Analysis

* Risk/activity distribution across episodes
* Episode-level risk breakdown with filters
* Risk-focused tables and comparisons

### Insights

* Event count trends
* Magnitude and activity behavior
* Distance and temporal analysis across episodes

### Visualizations

Includes multiple interactive charts such as:

* Magnitude distribution
* Monthly earthquake trends
* Depth vs. magnitude scatter plot
* Rolling event counts
* Time gap analysis
* Aftershock decay curves
* Episode spatial layout
* Global event map

### Predictions

Displays outputs from trained ML models for each episode:

* Probability of aftershock in the next 24 hours
* Expected next event magnitude
* Predicted time to next event
* Risk escalation category
* Estimated 7-day event count
* Cluster prediction

### Details

* Deep-dive view of selected episode
* Episode-specific metrics and behavior summary

### Raw Data

* Browse processed records
* Filter results
* Export selected data to CSV

---

## Project Structure

```bash
Earthquake-Aftershock-Dashboard/
│
├── app.py                          # Main Streamlit dashboard application
├── process_data.py                 # Data processing and episode generation pipeline
├── requirements.txt               # Project dependencies
│
├── data/
│   ├── raw/
│   │   └── query.csv              # Raw USGS earthquake dataset
│   └── processed/
│       ├── episode_summary.csv    # Episode-level summary data
│       └── final_earthquake_data.csv  # Processed event-level dataset
│
├── model/
│   ├── anomaly_model.pkl          # Isolation Forest anomaly model
│   ├── dbscan_model.pkl           # DBSCAN clustering model
│   └── predictions/               # Trained ML prediction models
│
└── src/
    ├── analyzer.py                # Risk scoring, clustering, and analysis logic
    ├── visuals.py                 # Plotly chart generation
    ├── feature_engineering.py     # Feature engineering pipeline
    └── prediction_models.py       # ML model training and prediction logic
```

---

## Installation

### 1. Clone the repository

```bash
git clone <your-repository-link>
cd Earthquake-Aftershock-Dashboard
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### Required packages

* streamlit
* pandas
* numpy
* scikit-learn
* matplotlib
* plotly
* joblib

---

## How to Run

### Step 1: Process the raw earthquake data

```bash
python process_data.py
```

This generates:

* processed CSV files in `data/processed/`
* trained models in `model/`

### Step 2: Launch the dashboard

```bash
streamlit run app.py
```

The dashboard will open in your browser, typically at:

```bash
http://localhost:8501
```

---

## Processing Pipeline

The `process_data.py` script performs the full data preparation workflow:

1. Loads raw earthquake records from CSV
2. Filters events by:

   * magnitude type (`mw`, `ml`, `mb`, `mww`, `mwr`)
   * network (`us`)
   * review status (`reviewed`)
3. Identifies **mainshock** events using magnitude thresholds
4. Groups nearby events into **aftershock episodes**
5. Computes temporal, spatial, and magnitude-based features
6. Applies clustering and anomaly detection
7. Calculates episode-level risk scores
8. Saves processed outputs for dashboard use and prediction tasks

---

## Episode Detection Logic

The project uses rule-based thresholds to define the radius and time window for aftershock grouping depending on the magnitude of the mainshock.

| Mainshock Magnitude | Radius | Time Window |
| ------------------- | ------ | ----------- |
| 7.0+                | 150 km | 30 days     |
| 6.0 – 6.9           | 100 km | 14 days     |
| 5.0 – 5.9           | 50 km  | 7 days      |

These thresholds are implemented in `process_data.py` and can be modified as needed.

Example:

```python
def get_radius_time(mag):
    if mag >= 7:
        return 150, 30
```

---

## Risk Scoring Methodology

Risk/activity scores are calculated at the episode level using a weighted combination of engineered features:

* **35%** event density
* **25%** event frequency
* **20%** geographic spread
* **20%** magnitude contribution

Risk labels are then assigned using score thresholds:

* **High Activity**
* **Moderate Activity**
* **Low Activity**

You can modify the thresholds in `src/analyzer.py`.

Example:

```python
if score > 0.7:
    return "High Activity"
elif score > 0.4:
    return "Moderate Activity"
else:
    return "Low Activity"
```

---

## Machine Learning Models

The project includes **six Random Forest-based models** trained on episode-level features.

### Models Included

1. **Aftershock Probability (24h)**
   Predicts whether an aftershock is likely to occur within the next 24 hours.

2. **Next Event Magnitude**
   Predicts the magnitude of the next event in the sequence.

3. **Time to Next Event**
   Estimates the time gap until the next earthquake event.

4. **Risk Escalation Prediction**
   Predicts whether the episode risk level is likely to escalate.

5. **7-Day Event Count Prediction**
   Estimates the number of earthquake events expected in the next 7 days.

6. **Spatial Cluster Prediction**
   Predicts the cluster/group to which an episode belongs.

### Features Used

The models use engineered episode-level features such as:

* lagged event statistics
* event trends
* velocity metrics
* spatial characteristics
* frequency and density measures

All features are normalized using **StandardScaler** before training.

---

## Technologies Used

* **Python**
* **Streamlit**
* **Pandas**
* **NumPy**
* **Scikit-learn**
* **Plotly**
* **Matplotlib**
* **Joblib**

---

## Configuration

### Change episode detection thresholds

Edit the `get_radius_time()` function in `process_data.py`.

### Change risk scoring thresholds

Edit the `risk_label()` logic in `src/analyzer.py`.

### Tune ML model parameters

Modify Random Forest hyperparameters in `src/prediction_models.py`, such as:

* `n_estimators`
* `max_depth`
* `min_samples_split`

---

## Troubleshooting

### Port already in use

Run Streamlit on a different port:

```bash
streamlit run app.py --server.port 8502
```

### Clear Streamlit cache

```bash
streamlit cache clear
```

### Regenerate processed data

```bash
python process_data.py
```

### Check Python version and installed packages

```bash
python --version
pip list
```

### Check for syntax issues

```bash
python -m py_compile app.py
```

---

## Performance Notes

* Data loading is cached for faster dashboard performance
* The first dashboard load may take a few seconds while models and processed data are loaded
* Subsequent interactions are faster unless the processed data is regenerated

---

## Future Improvements

Potential areas for enhancement:

* **Live USGS API integration** for real-time earthquake monitoring
* **Advanced deep learning models** for time-series forecasting
* **Alert/notification system** for high-risk aftershock sequences
* **Cloud deployment** for public access
* **Improved model evaluation dashboard**
* **Region-specific drill-down analytics**
* **Enhanced geospatial heatmaps and seismic trend analysis**

---

## Notes

* All timestamps are stored and processed in **UTC**
* Distances are calculated using the **Haversine formula**
* Processed outputs are designed for dashboard analytics and ML workflows
* The dashboard is intended for **analysis and exploration**, not operational seismic warning use

---

## Getting Started

1. Install dependencies

   ```bash
   pip install -r requirements.txt
   ```

2. Process the data

   ```bash
   python process_data.py
   ```

3. Start the dashboard

   ```bash
   streamlit run app.py
   ```

4. Explore the dashboard and export results as needed

---

## License

This project is intended for academic, learning, and portfolio purposes. Add a license here if you plan to make the repository public.
