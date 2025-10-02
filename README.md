#  Urban Traffic Flow Prediction

##  Problem Statement
Urban cellular networks face high traffic demand that can lead to congestion, slow data speeds, dropped connections, and poor user experience. Accurate traffic prediction is critical for **optimizing network resources**, **reducing latency**, **improving service quality**, and **planning future infrastructure upgrades**.

This project aims to **forecast traffic volumes** at each **beam** (cellular coverage area) using historical network data including **throughput volume, throughput time, PRB utilization, and user count**. Forecasting beam-level traffic allows operators to **anticipate congestion**, **balance load**, and **ensure consistent connectivity for users**.

---

## 📂 Dataset

- **Source:** [Zindi – Traffic Forecasting Challenge](https://zindi.africa/competitions/spatio-temporal-beam-level-traffic-forecasting-challenge/data)

- **Files & Description:**
  | File | Description |
  |------|-------------|
  | `traffic_DLThpVol.csv` | Throughput volume (target for forecasting) |
  | `traffic_DLThpTime.csv` | Throughput time / latency |
  | `traffic_DLPRB.csv` | Physical Resource Block utilization |
  | `traffic_MR_number.csv` | Number of active users |

- **Structure:**
  - Rows = 840 **beams** (cellular coverage sectors)
  - Columns = 2881 **time slots / timestamps**
  - Values = float64 measurements

- **Merged Dataset Example:**

| beam_id | timestamp | traffic_volume | throughput_time | PRB_utilization | user_count |
|---------|-----------|----------------|----------------|----------------|------------|
| 0       | 0_0_0     | 0.0            | 0.0            | 0.0667         | 0.0        |
| 1       | 0_0_0     | 0.0            | 0.0            | 0.1389         | 0.0        |
| 2       | 0_0_0     | 0.0            | 0.0149         | 0.1589         | 0.1735     |

---

##  Methodology

### 1️⃣ Preprocessing
- Merge all datasets on `beam_id` and `timestamp` → multivariate dataset
- Convert wide → long format for modeling
- Generate **sliding window sequences** to create multiple training samples per beam
- Normalize/scale features (MinMax or StandardScaler)
- Optional feature engineering: lag features, rolling averages, time-of-day indicators

### 2️⃣ Classical Models / Baselines
- **Basic ML Models:** Random Forest, Gradient Boosting, or XGBoost  
  - Treat each beam × timestamp row as a sample with features: `throughput_time`, `PRB_utilization`, `user_count`  
  - Provides a **baseline performance** for comparison
- **Time Series Models:** ARIMA / SARIMA / Prophet  
  - **Stationarity Check:** Augmented Dickey-Fuller (ADF) test; apply differencing or log transformation if non-stationary  
  - **Autocorrelation / Partial Autocorrelation:** Plot ACF / PACF to identify relevant lags for ARIMA/SARIMA  
  - **Seasonality Detection:** Detect daily/weekly patterns using seasonal decomposition
- **Evaluation Metrics:** RMSE / MAE

### 3️⃣ Unsupervised Learning
- **Clustering:** Group beams based on traffic patterns (high, medium, low congestion)
- **Dimensionality Reduction:** PCA / t-SNE / UMAP to visualize beam traffic patterns and cluster separation

### 4️⃣ Neural Networks / Deep Learning
- **LSTM / GRU:** Capture temporal dependencies in traffic sequences  
- Handles small dataset using **sliding windows, multivariate features, and regularization**

### 5️⃣ Evaluation
- **Metrics:** RMSE, MAE for traffic volume prediction
- **Visualization:** Compare predicted vs actual traffic volume over time and cluster separation of beams

---

##  Expected Impact
✅ **Network Optimization:** Predict congestion to allocate resources efficiently  
✅ **User Experience:** Reduce latency and improve data throughput  
✅ **Capacity Planning:** Anticipate high-demand periods and plan infrastructure  
✅ **Sustainability:** Optimize energy usage in cellular networks  

---

##  Deployment
- **Platform:** Streamlit
- **Features:**
  - Upload or visualize traffic data
  - Real-time traffic volume predictions per beam
  - Visualize congestion clusters and patterns

---

