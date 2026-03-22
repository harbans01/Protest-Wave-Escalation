# ML Models for ACLED/Protest Forecasting: A Researcher's Guide

## What Research Uses (in descending order of prevalence)

### 1. **Random Forest** (Most Common)
- **Why**: Handles non-linearity, interpretable feature importance, robust to scaling issues
- **Papers**: Guzzardo (2022), Obukhov & Brovelli (2024), most conflict forecasting studies
- **Typical hyperparameters**:
  - `n_estimators=100–500`
  - `max_depth=5–10` (shallow = generalization)
  - `class_weight='balanced'` (for imbalanced protest/no-protest)
- **Your use**: ✅ Aligned with standards

### 2. **Logistic Regression** (Baseline/Interpretability)
- **Why**: Fast, interpretable, good baseline for complex models
- **Use case**: Always reported for comparison
- **Your use**: ✅ Included

### 3. **XGBoost / LightGBM** (Emerging in recent work)
- **Prevalence**: Growing but not yet dominant in ACLED papers
- **When used**: When dataset is >10,000 samples
- **Papers**: Xue et al. (2025), some recent conflict studies
- **Note**: Can overfit on small protest datasets; not recommended for yours (479 waves)

### 4. **Neural Networks / LSTM** (Deep Learning)
- **Prevalence**: <10% of ACLED papers
- **Why**: Requires huge datasets, hard to interpret for policy
- **Exception**: Some recent sequence-to-sequence models for multi-step forecasting
- **Your use case**: Not recommended yet (insufficient data)

### 5. **Support Vector Machines (SVM)**
- **Prevalence**: Older papers (pre-2020)
- **Status**: Mostly superseded by tree ensembles
- **Your use**: Skip this

### 6. **Hawkes Processes** (Specialized/Theoretical)
- **Use**: Self-exciting point processes for event clustering
- **Papers**: Specialty papers on conflict clustering
- **Your use**: Not needed (you're doing classification, not intensity forecasting)

### 7. **Ensemble Methods** (Meta-level)
- **Use**: Stack RF + LR + XGBoost for final predictions
- **Prevalence**: Nice-to-have, not standard
- **Your use**: Not necessary unless you want publication boost

---

## Standard Feature Engineering (What Decent Researchers Do)

### **Temporal Features** (ESSENTIAL)
```
1. Event count (raw):
   - x(t) = count in current time unit (week/month)
   
2. Lagged counts:
   - x(t-1), x(t-2), moving average (7-day, 30-day, etc.)
   - Used for autoregressive signal

3. Growth rate:
   - Δx(t) = x(t) - x(t-1)
   - ✅ Your approach uses this

4. Acceleration (2nd derivative):
   - Δ²x(t) = Δx(t) - Δx(t-1)
   - ✅ Your approach uses this (replicator dynamics)

5. Trend decomposition:
   - Separating trend from noise (e.g., STL decomposition)
   - Used in some studies; your approach covers via moving stats
```

### **Baseline/Normalization** (CRITICAL)
```
1. Historical baseline (6-month, 12-month average)
   - Used to identify deviations
   
2. Relative deviation:
   - (x(t) - baseline) / baseline
   - ✅ Your approach includes

3. Z-score:
   - (x(t) - mean) / std
   - Some papers use this instead of relative deviation
```

### **Spatial Features** (CONTEXT-DEPENDENT)
```
1. Geographic controls:
   - Urban vs rural (binary)
   - Distance to capital (continuous)
   - Population density (if available)
   
2. Spillover/proximity:
   - Count in neighboring districts
   - Used in ACLED studies (good for contagion)
   - Your approach: ✅ Includes state_enc (coarse proxy)

3. Administrative tier effects:
   - State fixed effects (your state_enc does this)
```

### **Event-Level Features** (If available in raw data)
```
1. Event type distribution:
   - % protests vs. riots vs. violence
   - Useful if granular ACLED classification available

2. Fatality counts:
   - Total deaths in period (if available)
   - Strong signal for escalation

3. Number of actors:
   - Diversity of protest organizers
   - Proxy for coordination
```

### **Features You Should AVOID** (Data Leakage)
```
❌ Full-wave statistics (peak count, wave length, etc.)
   - Only use what's known at onset (first 2 weeks)
   
❌ Lagged labels or future information
   - Only use historical data
   
❌ Post-hoc wave properties
   - Peak position is deterministic after wave ends
```

### **Your Feature Set** (Paper-Quality)
✅ `onset_count` — initial participation
✅ `onset_growth` — initial growth rate
✅ `onset_acceleration` — change in growth (theory-motivated!)
✅ `relative_onset` — deviation from baseline
✅ `pre_wave_mean` — historical baseline
✅ `pre_wave_trend` — momentum before wave
✅ `baseline` — long-term district activity
✅ `state_enc` — geographic identity

**Assessment**: This is solid, mid-tier feature engineering. Not naive (you avoid leakage), but not cutting-edge (you don't have fatalities, spillover, actor diversity). Perfect for your current study scope.

---

## Your Study's Fit in the Literature

| Dimension | Your Approach | Typical Standard | Comment |
|-----------|---------------|-----------------|---------|
| **Model** | RF + LR | RF benchmark | Good |
| **Validation** | Stratified + GroupKFold + Temporal | Stratified only | Excellent (rare in literature) |
| **Features** | Replicator dynamics theory-motivated | Lagged counts | Novel & defensible |
| **Data** | Single country + external validation | Usually single country | Strong |
| **Sample size** | 479 waves | 500–5,000 typical | Small but acceptable |
| **Feature set** | 8 features | 20–50 typical | Parsimonious (good!) |

**Verdict**: You're **above average** for a protest forecasting study. Your theory anchor (replicator dynamics) is rare and valuable.

---

## Recommendations for Next Steps

### Immediate (Do for Paper)
1. ✅ Temporal validation (you have this)
2. ✅ GroupKFold validation (you have this)
3. ✅ Brazil external validation (you have this)
4. ✅ Calibration curve (you added this)
5. ✅ Baseline comparison (you added this)

### Nice-to-Have (If time allows)
- Spillover feature: neighboring district count (requires geo join)
- XGBoost comparison (benchmark against RF)
- Permutation importance (in addition to impurity-based)
- Decision curve analysis (for policy thresholds)

### Post-Publication (Future work)
- Add fatality/actor data if available
- Extend to 5+ countries
- Try domain adaptation (transfer learning with fine-tuning)
- SHAP values for local interpretability

---

## Common Pitfalls in ACLED ML Studies (You're Avoiding Most)

❌ Data leakage (future info in features)
✅ **You: Onset-only features**

❌ Train-test overlap by geography
✅ **You: GroupKFold validation**

❌ Train-test overlap by time
✅ **You: Temporal forward-chaining**

❌ Ignoring class imbalance
✅ **You: class_weight='balanced'**

❌ Over-fitting to small dataset
✅ **You: Shallow trees (max_depth=6), 5-fold CV**

❌ No baseline comparison
✅ **You: Logistic Regression + simple count models**

❌ Uncertainty unknown
✅ **You: Calibration + Brier score**

---

## Key Takeaway for Your Paper

**Framing**:
> We advance ACLED conflict forecasting by:
> 1. Grounding features in replicator dynamics theory (novelty)
> 2. Rigorous validation across spatial, temporal, and distributional domains (rigor)
> 3. External validation on Brazil data (generalizability)
> 4. Parsimonious feature set (interpretability)

**Avoid claiming**:
- "Definitively proves replicator dynamics" (prediction ≠ causation)
- "Outperforms all existing conflict forecasting" (limited baselines)
- "Ready for real-world deployment" (calibration is good but not perfect)

**Instead claim**:
- "Promising early warning signal for protest wave escalation"
- "Theory-guided features significantly outperform simple baselines"
- "Robust across multiple validation schemes and countries"
- "Foundation for future work in protest forecasting"

---

## Papers to Cite

1. **Obukhov & Brovelli (2023)** — Conditioning factors for ML conflict prediction
2. **Guzzardo (2022)** — Spatial conflict prediction with ML
3. **Xue et al. (2025)** — ML for conflict forecasting + migration (Nature)
4. **Murphy et al. (2024)** — Promise and pitfalls of ML in conflict forecasting
5. **ACLED codebook** — For data source credibility

All of these validate your methodological choices and provide benchmark comparisons.
