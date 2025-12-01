# suivi-datacamp
# Weekly Progress — Week 1

---

## Environment Setup

We successfully set up the required Python environment following the instructions from the starting kit. All dependencies were installed correctly, and the project structure is now ready for experimentation.

## Data Loading

We downloaded the public dataset using the provided script: `python download_data.py`

The data was loaded successfully, and we verified the structure and basic statistics of the single-cell RNAseq count data.

## Model Optimization

We started by optimizing the baseline Random Forest classifier provided in the starting kit. Using grid search, all team members tuned hyperparameters such as `n_estimators`, `max_depth`, and `max_features`.

The optimized model achieved the following performance:

* Training accuracy: **0.85**
* Test accuracy: **0.66**

While this represents an improvement over the baseline, the Random Forest still struggled with the high dimensionality of gene expression features.

---

# Exploration of Alternative Models

To address the limitations of the Random Forest, each team member implemented an alternative classifier and evaluated its performance using balanced accuracy.

### **Hangwei: Logistic Regression and XGBoost**

#### Logistic Regression:
* Train accuracy: 0.868
* Test accuracy: **0.730**

#### XGBoost:
* Train accuracy: 1.000
* Test accuracy: **0.791**

### **Ketong: LightGBM combined with high-variance gene selection**
* Train accuracy: 0.960
* Test accuracy: **0.740**

### **Louis: TruncatedSVD for dimensionality reduction followed by a Bagging classifier**
* Train accuracy: 0.985
* Test accuracy: **0.741**

Overall, all three alternative approaches outperformed the optimized Random Forest on the test set. This suggests that models using gradient boosting, dimensionality reduction, or simpler linear structures may generalize better to high-dimensional single-cell RNAseq data.

# Weekly Progress Report — Week 2 
#------------------**Hangwei's Part**----------------------
##  1. Data Preprocessing Optimization
To address the high dimensionality and sparsity (zero-inflation) issues encountered in Week 1, I implemented a robust preprocessing pipeline using **Scanpy**.

** New Pipeline Steps:**
1.  **Filtering:** Removed low-quality genes expressed in fewer than 3 cells.
2.  **Normalization:** Normalized total counts per cell to 10,000 (CPM).
3.  **Log Transformation:** Applied `log1p` to smooth the data distribution.
4.  **Feature Selection :** Selected the top **2,000 Highly Variable Genes** by ranking their standardized variance.
5.  **Scaling:** Applied `StandardScaler` to standardize features.

## **Results Comparison Raw vs. Optimized Data**

| Model Strategy | Test Bal. Acc (Raw Data) | Test Bal. Acc (Optimized) | Impact |
| :--- | :---: | :---: | :--- |
| **Random Forest** | 0.670 | **0.745** |  **+7.5%** (Significant Improvement) |
| **Logistic Regression** | 0.722 | **0.750** |  **+2.8%** (Improvement) |
| **XGBoost** | 0.822 | **0.828** |  **+0.6%** (No Significant Change) |

## **Result Interpretation**
The Scanpy preprocessing pipeline significantly improved the Random Forest and Logistic Regression models by removing noise and standardizing the features. However, XGBoost performance remained stable because tree-based boosting algorithms are inherently robust to raw, sparse data and high dimensionality, requiring less preprocessing to achieve optimal results.


#------------------**Ketong's Part**----------------------

Building on last week's baseline models, I further developed an optimized XGBoost-based denoising pipeline. This approach integrates systematic noise reduction, significance-driven feature extraction, and strong regularization strategies.
## 1. Feature Engineering: Constructing a Low-Noise, High-Information Feature Space
(1) Preprocessing: I applied a log(x + 1) transformation to stabilize variance in the raw expression matrix and performed L2 normalization on each cell to reduce scale differences caused by varying sequencing depth.

(2) Significant Feature Selection (SelectKBest — ANOVA F-value): I used ANOVA F-scores to assess feature relevance for the classification task and selected K = 2000 highly informative genes as the main input features.

(3) PCA for Noise Reduction: I applied PCA to the selected 2000 genes to extract stable latent components while suppressing stochastic noise, retaining the core biological signal for downstream modeling.
## 2. Model Construction & Parameter Optimization: XGBoost + Automated Grid Search
(1) Model Choice: XGBoost Classifier

(2) Automated Hyperparameter Optimization (Grid Search / Random Search): Optimal Parameters: xgb__reg_alpha = 1.0, xgb__n_estimators = 500, xgb__max_depth = 3, xgb__learning_rate = 0.03, pca__n_components = 50, feature_select__k = 2000.

(3) Training Strategy & Early Stopping: The dataset was split into 80% training and 20% fixed validation set for all hyperparameter combinations, and early stopping with 30 rounds was applied to prevent overfitting, with model selection guided by validation balanced accuracy.

## 3. Performance
| Metric                | Before Adjustment (Train 1.00, Valid 0.84) | After Adjustment (Train 0.92, Valid 0.81) | Improvement                                                             |
| --------------------- | ------------------------------------------ | ----------------------------------------- | ----------------------------------------------------------------------- |
| **Train Score**       | 1.00 ± 0.003                               | 0.92 ± 0.004                              | Training performance reduced — model no longer perfectly memorizes data |
| **Validation Score**  | 0.84 ± 0.014                               | 0.81 ± 0.015                              | Slight drop but more stable; variance remains low                       |
| **Overfitting Gap**   | 0.16 (1.00 − 0.84)                         | 0.11 (0.92 − 0.81)                        |  **Overfitting reduced by 31%**                                        |
| **Bagged Test Score** | 0.81                                       | 0.79                                      | Slight decrease but still strong, indicating robust generalization      |

The initial grid-search model performed strongly but showed clear overfitting.
Further adjustments to learning rate and L1 regularization strength (reg_alpha) improved stability.
