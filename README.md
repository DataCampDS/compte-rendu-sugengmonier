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
| **XGBoost** | **0.828** | 0.822 |  **-0.6%** (No Significant Change) |

## **Result Interpretation**
The Scanpy preprocessing pipeline significantly improved the Random Forest and Logistic Regression models by removing noise and standardizing the features. However, XGBoost performance remained stable because tree-based boosting algorithms are inherently robust to raw, sparse data and high dimensionality, requiring less preprocessing to achieve optimal results.
