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
