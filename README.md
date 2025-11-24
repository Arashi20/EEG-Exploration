# EEG Exploration + Probabilistic Modeling

A learning-focused project to familiarize myself with computational neuroscience concepts through hands-on EEG data analysis and Bayesian statistical modeling.

## 🎯 Learning Goals

This side project aims to:
- **Understand EEG signal processing**: Learn how to work with real brain data
- **Master preprocessing pipelines**: Filtering, artifact removal, and data cleaning
- **Explore brain activity patterns**: Visualize ERPs, frequency spectra, and topographic maps
- **Extract meaningful features**: Transform raw signals into interpretable metrics
- **Apply Bayesian inference**: Build probabilistic models to compare brain states
- **Develop computational neuroscience skills**: Bridge the gap between theory and practice

## 📂 Project Structure

```
EEG_Data_Project/
├── data/
│   └── preprocessed/          # Cleaned EEG data and extracted features
├── notebooks/
│   ├── data_exploration.ipynb      # Initial EEG visualization and data quality checks
│   ├── preprocessing.ipynb         # Filtering and ICA-based artifact removal
│   ├── erp_analysis.ipynb         # Event-related potential analysis
│   ├── feature_extraction.ipynb   # Band power and statistical feature extraction
│   └── bayesian_modeling.ipynb    # Hierarchical Bayesian models
└── README.md
```

## 🛠️ Technologies & Tools

- **MNE-Python**: EEG/MEG data loading, preprocessing, and visualization
- **PyMC**: Probabilistic programming for Bayesian inference
- **ArviZ**: MCMC diagnostics and posterior visualization
- **SciPy**: Signal processing (Welch's method for PSD)
- **NumPy/Pandas**: Data manipulation and feature organization
- **Matplotlib/Seaborn**: Plotting and visualization

## 📊 Dataset

**MNE Sample Dataset**: 
- Combined EEG/MEG recording from auditory-visual experiment
- 59 EEG channels used for analysis
- ~600 seconds of recording time
- Events: auditory and visual stimuli presented to left/right
- Pre-filtered (0-40 Hz) in the original dataset

## 🧠 Analysis Pipeline

### **1. Data Exploration** (`data_exploration.ipynb`)
- Loaded MNE sample dataset
- Visualized raw EEG signals (10 seconds of continuous data)
- Examined channel locations and electrode montage
- Analyzed power spectral density (PSD) to understand frequency content
- Identified brain wave bands: delta, theta, alpha, beta, gamma

**Key Learning**: Understanding the 1/f structure of brain signals and how different frequency bands relate to cognitive states.

---

### **2. ERP Analysis** (`erp_analysis.ipynb`)
- Found 319 stimulus events across 6 event types
- Created epochs: time-locked segments around each stimulus (-0.2 to +0.5s)
- Focused on two main conditions for comparison (72-73 epochs per condition)
- Averaged individual trials to create clean Event-Related Potentials (ERPs)
- Compared brain responses between conditions across multiple channels

**Key Learning**: How averaging reduces noise and reveals the brain's stereotypical response to stimuli.

---

### **3. Preprocessing** (`preprocessing.ipynb`)
- Applied bandpass filter (0.1-30 Hz) to remove slow drifts and high-frequency noise
- Loaded data with EOG channels for eye artifact detection
- Fitted ICA with 15 components on filtered EEG data
- Automatically detected eye blink components using EOG correlation
- Removed artifact components and saved cleaned data

**Result**: Clean EEG data saved to `data/preprocessed/sample_clean_eeg_raw.fif`

**Key Learning**: The original MNE sample data was already pre-filtered (0-40 Hz), so additional filtering had minimal effect. ICA successfully identified and removed eye blink artifacts.

---

### **4. Feature Extraction** (`feature_extraction.ipynb`)
- Loaded cleaned EEG data from preprocessing
- Created 145 epochs (72 condition_1, 73 condition_2) for feature extraction
- Extracted **statistical features** per channel:
  - Mean, standard deviation, variance
  - Min, max, range
  - Skewness, kurtosis
  - Zero crossings (signal complexity)
  
- Extracted **spectral features** per channel:
  - Band power: delta (0.5-4 Hz), theta (4-8 Hz), alpha (8-13 Hz), beta (13-30 Hz), gamma (30-45 Hz)
  - Relative band powers (normalized by total power)
  - Total power
  
- Processed all 59 EEG channels → **1,180 features per epoch**
- Saved features to CSV: `data/preprocessed/eeg_features.csv`

**Result**: Feature matrix with 145 rows (epochs) × 1,182 columns (features + labels)

**Key Learning**: Transforming raw signals into meaningful features bridges the gap between neuroscience (brain oscillations) and machine learning (numerical features).

---

### **5. Bayesian Modeling** (`bayesian_modeling.ipynb`)

#### **Model 1: Simple Bayesian T-Test** (Single Channel)
- Compared alpha power between two conditions for channel EEG 001
- Standardized data (z-scores) for better MCMC sampling
- Built Bayesian model with priors for group means and standard deviations
- Sampled posterior distribution (1000 samples, 2 chains)
- Calculated probability of difference and effect size (Cohen's d)

**Finding**: 
- 80% probability Condition 2 > Condition 1
- Small effect size (d = -0.14)
- 95% credible interval crosses zero → weak evidence
- **Conclusion**: The two conditions show similar alpha power in this channel

**Key Learning**: Bayesian inference provides probabilistic statements ("80% probability") instead of binary yes/no decisions, with full uncertainty quantification.

---

#### **Model 2: Hierarchical Bayesian Model** (All 59 Channels)
- Standardized alpha power features across all 59 channels
- Built hierarchical model with:
  - **Population level**: Brain-wide average effect (μ_population, σ_population)
  - **Channel level**: 59 individual channel effects that "borrow strength" from population
- Sampled with 50 samples per chain due to computational constraints

**Attempted Result**: 
- Population effect suggested Condition 2 > Condition 1 (mean = -0.085)
- Frontal channels (EEG 004, 005) showed strongest effects

**⚠️ CRITICAL LIMITATION**:
- **Non-convergence**: R-hat = 1.13 (μ), 2.26 (σ) >> ideal of 1.01
- Only 50 MCMC samples (need 1000+) due to time constraints (~20 minutes per run)
- 19 divergences during sampling
- **Results are exploratory only and NOT statistically reliable**

**Key Learning**: 
- Hierarchical models are powerful but computationally expensive
- Proper MCMC diagnostics (R-hat, trace plots, divergences) are essential
- Real research involves trade-offs between ideal methods and practical constraints
- Honest reporting of limitations is crucial in science

---

## 🎓 Key Takeaways

### What I Learned ✅

1. **EEG Signal Processing**:
   - How to work with MNE-Python for real brain data
   - Understanding frequency bands and their cognitive significance
   - Visualizing brain activity through ERPs and topographic maps

2. **Preprocessing Challenges**:
   - ICA effectively removes eye artifacts
   - Filtering choices depend on research questions
   - Many public datasets are already pre-cleaned

3. **Feature Engineering**:
   - Statistical features capture signal shape and variability
   - Spectral features capture brain rhythms and oscillations
   - Both time and frequency domains provide complementary information

4. **Bayesian Inference**:
   - Probabilistic statements are more informative than p-values
   - Hierarchical models enable multi-level analysis
   - Convergence diagnostics are non-negotiable
   - Computational constraints are a real limitation

5. **Scientific Practice**:
   - Limitations should be reported as prominently as results
   - Exploratory analysis is valuable even without perfect convergence
   - The learning process is as important as the final results

### Computational Challenges 🚧

- **MCMC sampling is slow**: 59 channels × 72 epochs → ~14 seconds per sample
- **Memory requirements**: Full hierarchical model requires significant RAM
- **Trade-offs**: Had to reduce samples (50 vs ideal 1000+) for feasibility
- **Lesson**: Model complexity must match available compute resources

### If I Were to Continue... 🚀

1. **Increase MCMC samples**: Run overnight with 1000+ samples, 4 chains
2. **Use region-of-interest analysis**: Group channels by brain region to reduce parameters
3. **Try variational inference**: PyMC's ADVI for faster approximate Bayesian inference
4. **Explore other features**: Connectivity measures, time-frequency analysis
5. **Multi-feature models**: Combine multiple frequency bands in one hierarchical model

---

## 📚 Resources That Helped

- [MNE-Python Tutorials](https://mne.tools/stable/auto_tutorials/index.html)
- [PyMC Documentation](https://www.pymc.io/welcome.html)
- [Bayesian Data Analysis - Gelman et al.](http://www.stat.columbia.edu/~gelman/book/)
- Mike X Cohen - *Analyzing Neural Time Series Data*

---

## 🎯 Conclusion

This project successfully demonstrated the full pipeline from raw EEG signals to Bayesian statistical inference. While computational constraints limited the final model's reliability, the learning objectives were fully achieved:

- ✅ Hands-on experience with real brain data
- ✅ Understanding of preprocessing and artifact removal
- ✅ Feature extraction from time-series signals
- ✅ Application of hierarchical Bayesian modeling
- ✅ Proper model diagnostics and limitation reporting

The journey from downloading data to building complex probabilistic models provided invaluable experience in computational neuroscience. The key lesson: scientific integrity means being transparent about what worked, what didn't, and why.

---

**Note**: This is a learning project emphasizing methodology and the research process. Results are exploratory due to computational constraints on MCMC sampling. The focus is on developing skills in computational neuroscience rather than producing publication-ready findings.

