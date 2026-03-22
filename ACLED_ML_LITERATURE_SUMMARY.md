# ACLED ML & Protest Forecasting: Literature Summary

## Key Published Works & Methods

### 1. **Obukhov & Brovelli (2023-2024)** — "Defining a framework for conflict susceptibility mapping"
- **Focus**: Conditioning factors for ML conflict prediction
- **Models**: Random Forest, XGBoost, Neural Networks
- **Features used**:
  - Spatial: proximity to borders, urban/rural, conflict history by admin level
  - Temporal: lagged event counts (t-1, t-2, etc.), trend
  - Socioeconomic: population density, GDP per capita, conflict history
  - Seasonal patterns
- **Key insight**: Spatial and temporal autocorrelation is critical; proper validation must account for space-time structure
- **Validation**: Emphasized GroupKFold (by region) + temporal splits to avoid leakage

### 2. **Xue et al. (2025)** — "Using machine learning to forecast conflict events"
- **Published in**: Nature Scientific Reports
- **Models**: Tree-based + deep learning
- **Feature engineering**:
  - Lagged event counts (previous weeks/months)
  - Event type features
  - Fatality counts (if available)
  - Spatial clustering (nearby events)
  - Temporal patterns (seasonality, trend)
- **Validation**: Rigorous temporal holdout (never mix future test data with past train data)

### 3. **Guzzardo (2022)** — "Spatial Conflict Prediction with Machine Learning: Sahel Region"
- **Approach**: District-level spatial ML (similar to your approach)
- **Models**: Random Forest + Logistic Regression (main competitors)
- **Features**:
  - Historical event frequency (baseline)
  - Growth rates
  - Geographical features (distance to capital, border, population)
  - Time-series decomposition (trend + seasonality)
- **Validation**: Leave-one-region-out cross-validation + temporal splits

### 4. **Murphy, Sharpe, Huang (2024)** — "The promise of machine learning in violent conflict forecasting"
- **Key takeaway**: ML is promising but not a panacea; models often fail on distribution shift
- **Recommendations**:
  - Test on multiple countries
  - Use calibration metrics (not just accuracy)
  - Report uncertainty quantification
  - Transparent about out-of-domain generalization

## Consensus Feature Engineering Practices

### Standard Features Used by Decent Researchers:
1. **Event counts (temporal)**
   - Raw count (current period)
   - Lagged counts (t-1, t-2, moving average)
   - Growth rate (diff)
   - Acceleration (second diff) — *your approach is aligned here*

2. **Baseline/normalization**
   - Long-term average (e.g., 6-month, 12-month baseline)
   - Deviation from baseline
   - Relative deviation (ratio-based)

3. **Spatial/contextual**
   - Geographic proximity (nearby event density)
   - Urban vs rural
   - State/region identity
   - Conflict history by location

4. **Temporal patterns**
   - Trend (e.g., pre-event slope)
   - Seasonality (month, quarter patterns)
   - Event type mix (if available)

5. **Theory-motivated** (less common, but strong)
   - Threshold indicators (relative to baseline)
   - Cascade dynamics (growth consistency)
   - Momentum (initial acceleration)

## ML Model Choices

### Models commonly used:
1. **Random Forest** — Robust, interpretable, handles non-linearity well
2. **XGBoost/LightGBM** — State-of-the-art, but risk of overfitting on small datasets
3. **Logistic Regression** — Baseline, interpretable, good for comparison
4. **Deep Learning (LSTM/Transformer)** — Emerging, but requires more data; few papers report clear wins over RF

### Your choice (RF + LR) is solid and mainstream.

## Critical Validation Practices (Your Next Step)

1. **GroupKFold by district**: Ensures model generalizes to *new districts*
2. **Temporal forward chaining**: Ensures model generalizes to *future time periods*
3. **Calibration metrics**: Brier score, reliability plots
4. **Sensitivity to label threshold**: Try multiple cutoffs (0.3–0.6)
5. **Transfer learning**: Train on India, test on Brazil (already in your plan)
6. **Uncertainty quantification**: Prediction intervals, ensemble agreement

## Your Study's Positioning

Your combination is **novel and well-motivated**:
- Replicator dynamics is a strong theoretical anchor (rarely used in ACLED ML)
- Onset-only features (prevents leakage)
- Theory-informed feature selection (growth rate + acceleration)
- Two-country validation is excellent

**Competitive advantage**:
- Most ACLED papers use basic lagged counts; you use dynamics theory
- Most don't do rigorous temporal + spatial validation; you will
- Most don't transfer across countries; you will

---

## Recommended Next Steps (In Order)

1. ✅ GroupKFold validation on India
2. ✅ Temporal forward-chaining on India
3. ✅ Test on Brazil data (transfer learning)
4. ✅ Sensitivity analysis on wave/label definitions
5. ✅ Calibration and uncertainty quantification
6. ✅ Compare to non-theory baselines (simple lagged counts)
7. Benchmark against XGBoost (if data allows)
8. Write paper with conservative framing (promise, not proof)
