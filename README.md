# Cellular Traffic Volume Prediction per Beam

## Problem Statement

Urban cellular networks face high traffic demand that can lead to congestion, slow data speeds, dropped connections, and poor user experience. Accurate traffic prediction is critical for optimizing network resources, reducing latency, improving service quality, and planning future infrastructure upgrades.

This project aims to forecast traffic volumes at each beam (cellular coverage area) using historical network data including throughput volume, throughput time, PRB utilization, and user count. Forecasting beam-level traffic allows operators to anticipate congestion, balance load, and ensure consistent connectivity for users.

## Dataset

**Source:** [Zindi – Spatio-Temporal Beam-Level Traffic Forecasting Challenge](https://zindi.africa/competitions/spatio-temporal-beam-level-traffic-forecasting-challenge/data)

**Files & Description:**

| File                     | Description                                |
|--------------------------|--------------------------------------------|
| `traffic_Vol.csv`        | Historical traffic volume per beam         |
| `traffic_DLThpTime.csv`  | Downlink throughput time (network load)    |
| `traffic_DLPRB.csv`      | PRB utilization per beam                    |
| `traffic_MR_number.csv`  | Number of active users per beam             |

**Structure:**

- Beams: 840
- Temporal granularity: Hourly
- Columns: 2881 time slots / timestamps
- Values: float64 measurements

**Merged Dataset Example:**

| beam_id | timestamp | traffic_volume | throughput_time | PRB_utilization | user_count |
|---------|-----------|----------------|----------------|----------------|------------|
| 0_0_0   | 0         | 0.0            | 0.0            | 0.0667         | 0.0        |
| 0_0_0   | 1         | 0.0            | 0.0            | 0.1389         | 0.0        |
| 0_0_0   | 2         | 0.0            | 0.0149         | 0.1589         | 0.1735     |

## Exploratory Data Analysis (EDA)

EDA was performed to understand data and feature relationships:

- Heatmap: Checked multicollinearity between features.
- Histograms: Distribution of traffic volume, throughput, PRB utilization, user count.
- Boxplots: Identified outliers.
- Pairplots: Examined linearity and correlation between features.
- Time Series Decomposition: Trend and seasonal patterns per beam.

## Feature Engineering

- Lag features: `lag1`, `lag2` – traffic from previous hours.
- Rolling mean: `roll3` – smooth short-term fluctuations.
- Cyclical time features: `sin_time`, `cos_time` – hourly pattern.
- Other metrics: `throughput_time`, `PRB_utilization`, `user_count`.

## Modeling Approach

Multiple models were trained and evaluated:

| Model       | Features                                      | Notes                                         |
|------------|-----------------------------------------------|-----------------------------------------------|
| Random Forest | lag1, lag2, roll3, throughput_time, PRB_utilization, user_count, sin_time, cos_time | Captures non-linear relationships; fast inference |
| XGBoost     | Same as RF                                   | Gradient boosting; good baseline for tabular data |
| ARIMA / SARIMA | traffic_volume time series per beam        | Captures trend and seasonality for single beams |
| LSTM        | Same as RF, sequence input                   | Captures temporal dependencies; performs well on sequence data |

## Model Performance

### Before Hyperparameter Tuning

| Model         | MSE (Test) | MAE (Test) | R² (Test) |
|---------------|------------|------------|-----------|
| Random Forest | 0.0015     | 0.035      | 0.985     |
| XGBoost       | 0.0020     | 0.045      | 0.980     |
| LSTM          | 0.0075     | 0.045      | 0.915     |
| ARIMA         | 0.020      | 0.110      | 0.860     |
| SARIMA        | 0.018      | 0.105      | 0.870     |

### After Hyperparameter Tuning

| Model                 | MSE (Test) | MAE (Test) | R² (Test) |
|-----------------------|------------|------------|-----------|
| Random Forest (Tuned) | 0.0001     | 0.0030     | 0.9984    |
| LSTM                  | 0.0050     | 0.0253     | 0.9145    |

> Best model: Tuned Random Forest (R² = 0.9984, very low error)

## Deployment – Streamlit App

- Users can select a beam ID.
- Set input features via sliders.
- Predict traffic volume and see traffic level (Low / Medium / High).
- View beam statistics for context.

**Example Features for User Input:**

- Lag1, Lag2: previous traffic volumes
- Rolling Mean (3): average of last 3 traffic volumes
- Throughput Time: network load (ms)
- PRB Utilization: percentage of resource blocks used
- User Count: active users per beam
- Hour: hour of day (converted to sine/cosine)

## Challenges

- Large dataset size (840 beams × thousands of timestamps)
- Merging multiple CSVs per beam with aligned timestamps
- Model selection: balancing accuracy and inference speed
- LSTM deployment: handling `.h5` vs `.keras` file formats
- Feature correlation and outlier handling

## Future Work

- Real-time prediction with live network logs
- Advanced hyperparameter tuning (Bayesian optimization)
- Additional features: weather, mobility, handovers
- Model explainability (SHAP values, feature importance)
- Beam clustering to reduce model complexity

## Project Workflow
Raw Data CSVs
   │
   ├─> Merge & Melt → df_long
   │
   ├─> EDA
   │     ├─ Heatmap (Multicollinearity)
   │     ├─ Histograms (Distribution)
   │     ├─ Boxplots (Outliers)
   │     └─ Pairplots (Linearity)
   │
   ├─> Feature Engineering
   │     ├─ Lags: lag1, lag2
   │     ├─ Rolling Mean: roll3
   │     └─ Cyclical Time: sin_time, cos_time
   │
   ├─> Train / Val / Test Split
   │
   ├─> Model Training
   │     ├─ Random Forest (Best)
   │     ├─ XGBoost
   │     ├─ ARIMA / SARIMA
   │     └─ LSTM
   │
   ├─> Evaluation (MSE, MAE, R²)
   │
   └─> Streamlit App
         ├─ User selects Beam & Features
         ├─ Predict Traffic Volume
         ├─ Show Traffic Level (Low/Medium/High)
         └─ Display Beam Stats

Raw Data CSVs
│
├─> Merge & Melt → df_long
│
├─> EDA
│ ├─ Heatmap (Multicollinearity)
│ ├─ Histograms (Distribution)
│ ├─ Boxplots (Outliers)
│ └─ Pairplots (Linearity)
│
├─> Feature Engineering
│ ├─ Lags: lag1, lag2
│ ├─ Rolling Mean: roll3
│ └─ Cyclical Time: sin_time, cos_time
│
├─> Train / Val / Test Split
│
├─> Model Training
│ ├─ Random Forest (Best)
│ ├─ XGBoost
│ ├─ ARIMA / SARIMA
│ └─ LSTM
│
├─> Evaluation (MSE, MAE, R²)
│
└─> Streamlit App
├─ User selects Beam & Features
├─ Predict Traffic Volume
├─ Show Traffic Level (Low/Medium/High)
└─ Display Beam Stats


## References

- [Scikit-learn Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#forest)
- [XGBoost Documentation](https://xgboost.readthedocs.io/)
- [Keras LSTM Guide](https://keras.io/api/layers/recurrent_layers/lstm/)
- Network monitoring logs from the telecom operator (internal source)


