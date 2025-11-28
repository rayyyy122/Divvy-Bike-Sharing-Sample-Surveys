# Statistical Analysis of Chicago Divvy Bike-Share System

A comprehensive statistical analysis of the Divvy bike-sharing system in Chicago using Simple Random Sampling (SRS) and Stratified Sampling methods to estimate key operational metrics and evaluate system performance.

## 📋 Project Overview

This project investigates whether Chicago's Divvy bike-sharing system effectively serves as a viable urban transportation option by analyzing ride duration patterns and electric bike adoption rates. Using classical survey sampling techniques, we estimate population parameters from a representative sample of March 2023 ride data.

### Research Questions

1. **Average Ride Duration**: Do users primarily use Divvy bikes for short commutes (suggesting effective urban integration) or longer recreational rides?
2. **Electric Bike Adoption**: How popular are newly introduced electric bikes compared to traditional docked bicycles?

## 🎯 Key Findings

| Parameter | Estimate | 95% CI | Method |
|-----------|----------|--------|---------|
| **Mean Ride Duration** | 11.10 minutes | [9.97, 12.23] | SRS (Ratio) |
| **Mean Ride Duration** | 11.62 minutes | [10.95, 12.29] | Stratified |
| **Electric Bike Proportion** | 44.8% | [39.7%, 49.9%] | SRS (Vanilla) |
| **Electric Bike Proportion** | 41.7% | [39.2%, 44.2%] | Stratified |

### Main Conclusions

- **Short-distance commuting dominates**: Average ride duration of ~11 minutes suggests users primarily use Divvy for last-mile connections rather than long-distance transit
- **Electric bikes gaining traction**: Approximately 42-45% of rides use electric bikes, indicating strong adoption of the newer technology
- **Minimal member/casual variance**: Small differences between annual members and casual users suggest similar usage patterns across user types

## 📊 Dataset

**Source**: [Divvy Trip Data (March 2023)](https://divvy-tripdata.s3.amazonaws.com/index.html)  
**Provider**: Motivate International Inc. (Divvy)  
**License**: [Data License Agreement](https://ride.divvybikes.com/data-license-agreement)

### Dataset Characteristics

- **Raw observations**: 258,678 rides
- **Clean observations**: 194,503 rides (after preprocessing)
- **Time period**: March 2023
- **Geographic coverage**: Chicago metropolitan area

### Variables

| Variable | Type | Description |
|----------|------|-------------|
| `ride_id` | Categorical | Unique ride identifier |
| `rideable_type` | Categorical | Bike type (classic_bike, electric_bike, docked_bike) |
| `started_at` | Datetime | Ride start timestamp |
| `ended_at` | Datetime | Ride end timestamp |
| `start_station_name` | Categorical | Starting station name |
| `start_station_id` | Categorical | Starting station ID |
| `end_station_name` | Categorical | Ending station name |
| `end_station_id` | Categorical | Ending station ID |
| `start_lat` | Numeric | Starting latitude |
| `start_lng` | Numeric | Starting longitude |
| `end_lat` | Numeric | Ending latitude |
| `end_lng` | Numeric | Ending longitude |
| `member_casual` | Categorical | User type (member/casual) |
| `duration`* | Numeric | Ride duration in minutes |
| `displacement`* | Numeric | Haversine distance between start/end (meters) |

*\* Derived variables*

### Data Preprocessing

1. **Missing value removal**: Dropped all rows with NA or empty values
2. **Duration calculation**: Computed as `ended_at - started_at` (in minutes)
3. **Outlier filtering**: Removed rides < 1 minute (likely faulty equipment)
4. **Auxiliary variable creation**: Calculated displacement using Haversine formula
5. **Final sample**: 194,503 valid rides

## 🔬 Methodology

### Sampling Design

**Target Population**: All Divvy bike rides in Chicago during March 2023

**Sample Size Calculation** (95% confidence, 5% margin of error):
```
n₀ = (1.96)² × 0.5 × 0.5 / 0.05² = 384.16
n = n₀ / (1 + n₀/N) = 384.16 / (1 + 384.16/194,503) ≈ 384
```

### Statistical Methods

#### 1. Simple Random Sampling (SRS)

**Vanilla Estimator**:
- Mean duration: ȳ = Σyᵢ/n
- SE: √[(1 - n/N) × s²/n]

**Ratio Estimator**:
- Uses displacement as auxiliary variable
- Estimator: (ȳ/x̄ₛ) × x̄ₚ
- SE: √[(1 - n/N) × s²ₑ/n]

#### 2. Stratified Sampling

**Strata Definition**:
- **Stratum M**: Annual members (N_m = 148,773)
- **Stratum C**: Casual users (N_c = 45,730)

**Proportional Allocation**:
- n_m = 294 (members)
- n_c = 90 (casual users)

**Estimator**: ȳₛₜᵣ = (N_m/N)ȳ_m + (N_c/N)ȳ_c

### Auxiliary Variable: Displacement

Created as additional predictor for ratio estimation:
- Calculated using Haversine distance formula
- Hypothesis: Longer displacements correlate with longer ride durations
- Result: Strong correlation confirmed (ratio estimator reduced SE by 7%)

## 📈 Results & Analysis

### Parameter 1: Mean Ride Duration

| Method | Estimate (min) | SE | 95% CI |
|--------|----------------|-----|---------|
| SRS (Vanilla) | 11.04 | 0.621 | [9.83, 12.26] |
| **SRS (Ratio)** | **11.10** | **0.576** | **[9.97, 12.23]** |
| Stratified | 11.62 | 0.669 | [10.95, 12.29] |

**Analysis**:
- Ratio estimator outperformed vanilla (7% SE reduction) → strong auxiliary variable correlation
- Stratified sampling showed *larger* SE than SRS → minimal between-strata variance
- Conclusion: Both members and casual users exhibit similar ride duration patterns

**Interpretation**: Average ride duration of ~11 minutes indicates Divvy primarily serves short-distance commuting and last-mile connections rather than replacing buses/trains for longer trips.

### Parameter 2: Proportion of Electric Bikes

| Method | Estimate | SE | 95% CI |
|--------|----------|-----|---------|
| **SRS (Vanilla)** | **44.8%** | **2.54%** | **[39.7%, 49.9%]** |
| SRS (Ratio) | 45.0% | 3.14% | [38.9%, 51.2%] |
| Stratified | 41.7% | 2.51% | [39.2%, 44.2%] |

**Analysis**:
- Vanilla estimator outperformed ratio → weak correlation between bike type and displacement
- Stratified sampling performed similarly to vanilla SRS
- Conclusion: Electric bike selection appears random/availability-driven

**Interpretation**: Electric bikes account for 42-45% of all rides, demonstrating strong adoption despite being newer to the fleet. This suggests users value the convenience when available.

## 🛠️ Installation & Usage

### Prerequisites

```r
# Required R version
R >= 4.0.0

# Install required packages
install.packages(c(
  "REdaS",
  "tidyverse", 
  "infer",
  "tibble",
  "geosphere"
))
```

### Running the Analysis

```bash
# 1. Clone the repository
git clone https://github.com/yourusername/divvy-statistical-analysis.git
cd divvy-statistical-analysis

# 2. Download the dataset
# Download from: https://divvy-tripdata.s3.amazonaws.com/202303-divvy-tripdata.zip
# Extract and place '202303-divvy-tripdata.csv' in project root

# 3. Run the analysis
Rscript project.R
```

### Reproducing Results

```r
# Set working directory
setwd("path/to/project")

# Load data and run analysis
source("project.R")

# All estimates will be computed with set.seed(0) for reproducibility
```

## 📁 Project Structure

```
divvy-statistical-analysis/
├── README.md
├── STAT_344_Project.pdf          # Comprehensive project report
├── project.R                      # R analysis script
├── 202303-divvy-tripdata.csv     # Dataset (download separately)
└── figures/                       # Generated visualizations (if any)
```

## 🔍 Methodological Insights

### Why Ratio Estimator Outperformed for Duration

The ratio estimator achieved 7% SE reduction because:
1. **Strong correlation**: Displacement and duration show positive linear relationship (r ≈ 0.7)
2. **Known population mean**: We know x̄ₚ (mean displacement) from full dataset
3. **Variance reduction**: Residual variance s²ₑ < sample variance s²

### Why Stratified Sampling Underperformed

Stratified sampling showed *larger* SE than SRS because:
1. **Low between-strata variance**: Members and casual users behave similarly
2. **High within-strata variance**: Individual ride patterns vary substantially
3. **Optimal allocation not achieved**: Fixed sample size prevented optimal allocation

**Formula for stratified efficiency**:
```
Efficiency ∝ Σ(Wₕ × σₕ)² / σ²ₚₒₚ
```
When within-strata variances (σₕ) are similar to population variance (σₚₒₚ), stratification provides minimal benefit.

## 📊 Statistical Inference Summary

### Confidence Interval Interpretation

For **mean ride duration** (ratio estimate):
> "We are 95% confident that in 19 out of 20 repeated samplings, the interval [9.97, 12.23] minutes will capture the true population mean ride duration."

For **electric bike proportion** (vanilla estimate):
> "We are 95% confident that in 19 out of 20 repeated samplings, the interval [39.7%, 49.9%] will capture the true population proportion of electric bike rides."

### Design Effect

Comparing stratified vs SRS for proportion:
```
DEFF = (SE_stratified / SE_SRS)² = (0.0251 / 0.0254)² ≈ 0.98
```
DEFF < 1 indicates slight efficiency gain from stratification, but minimal.

## 🎓 Academic Context

**Course**: STAT 344 - Survey Sampling  
**Institution**: University of British Columbia  
**Date**: November 2023  
**Team**: Dingrui (Ray) Tao (Lead), Mengfei Chen

### Learning Outcomes Demonstrated

- Survey sampling design and implementation
- Comparison of estimation methods (vanilla, ratio, stratified)
- Auxiliary variable selection and evaluation
- Standard error computation and confidence interval construction
- Practical interpretation of statistical results in urban planning context

## 🚀 Future Work

### Methodological Extensions

1. **Temporal Analysis**: Analyze all 12 months (May 2022 - April 2023) for seasonal patterns
2. **Optimal Allocation**: Implement Neyman allocation for stratified sampling
3. **Cluster Sampling**: Use station-based clusters for spatial analysis
4. **Regression Estimation**: Develop linear models for more efficient estimation

### Research Questions

1. **Peak hour analysis**: How does ride duration vary by time of day?
2. **Weather effects**: Impact of temperature/precipitation on usage patterns
3. **Spatial patterns**: Geographic clustering of electric bike usage
4. **User segmentation**: Behavioral differences between member types

### Predictive Modeling

- Time series forecasting for demand prediction
- Spatial modeling for optimal bike redistribution
- Classification models for bike type preference

## 📝 Limitations

1. **Single month analysis**: Results limited to March 2023, may not generalize across seasons
2. **Missing data**: Unknown reasons for NA/empty values; potential selection bias
3. **Simplified auxiliary variable**: Displacement doesn't account for actual route taken
4. **Population assumption**: Treated sample as population for parameter estimation
5. **Binary stratification**: Only used member/casual; could explore multi-way stratification
6. **Independence assumption**: Rides may be temporally correlated (violated i.i.d. assumption)

## 📚 References

**Data Source**:
- Divvy Bikes. (2023). *Divvy Trip Data*. Motivate International Inc. Retrieved from https://divvy-tripdata.s3.amazonaws.com/index.html

**Statistical Methods**:
- Lohr, S. L. (2010). *Sampling: Design and Analysis* (2nd ed.). Brooks/Cole.
- Cochran, W. G. (1977). *Sampling Techniques* (3rd ed.). John Wiley & Sons.

**Related Work**:
- Perlman, M. D., & Wu, L. (1999). The Emperor's New Tests. *Statistical Science*, 14(4), 355-369.

## 👥 Contributors

- **Dingrui (Ray) Tao** - Team Leader, Data Collection, Report Writing, R Programming
- **Mengfei Chen** - Team Member, Data Collection, LaTeX Formatting, R Programming

## 📄 License

This project is for educational purposes. The dataset is provided by Motivate International Inc. under their [Data License Agreement](https://ride.divvybikes.com/data-license-agreement).

## 🙏 Acknowledgments

- Divvy Bikes and Motivate International Inc. for providing open data
- STAT 344 instructors for methodology guidance
- City of Chicago Department of Transportation (CDOT) for supporting bike-share initiatives

---

**Note**: This project demonstrates practical application of survey sampling techniques to real-world urban transportation data, emphasizing the importance of appropriate estimator selection and the trade-offs between different sampling designs.
