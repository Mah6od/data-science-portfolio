# 🧠 Developer Seniority Prediction (XGBoost + BorderlineSMOTE Pipeline)

## 📘 Overview
This project aims to predict a developer’s **seniority level** (`Junior`, `Mid-level`, `Senior`) based on their professional background and job-related features.  
The dataset (`developers.csv`) includes columns such as job titles, experience, salary, company details, and employment type.

We use an **XGBoost Classifier** within a hybrid pipeline that handles:
- feature extraction from job titles,
- numerical and categorical preprocessing,
- imbalanced data correction using **BorderlineSMOTE**, and
- hyperparameter optimization using **RandomizedSearchCV**.

---

## ⚙️ Model Pipeline
The full ML pipeline includes the following stages:

### 1️⃣ Feature Engineering
**From Title → Extract Seniority & Specialty**
- Custom text parsers map each job title to a *Seniority* and *Specialty*.
- Helper functions extract text-based features such as:
  - `has_lead`, `has_manager_word`, `has_senior_word`, `has_junior_word`, `has_architect`
  - `title_len`, `title_word_count`

**Example:**
Title = "Senior Back-End Developer"
→ Seniority = "Senior"
→ Specialty = "Back-End"
→ has_senior_word = 1
→ title_len = 24
→ title_word_count = 3


### 2️⃣ Preprocessing
Features are separated into **numerical** and **categorical** columns:

| Step | Transformation | Technique |
|------|----------------|-----------|
| Missing values | Imputation | SimpleImputer (median or constant) |
| Numerical scaling | Normalization | StandardScaler |
| Categorical encoding | One-hot encoding | OneHotEncoder |
| Log-transform target | Salary normalization | np.log1p(AvgSalary) |

Transformations are combined using a `ColumnTransformer` inside a scikit-learn pipeline.

### 3️⃣ Handling Imbalanced Classes
- Minority classes (e.g., Junior developers) are oversampled using **BorderlineSMOTE**.
- Custom sampling strategy avoids overfitting (max 60% of majority class size).

### 4️⃣ Model Training
- **XGBoostClassifier** with `multi:softprob` objective for multiclass classification.
- **RandomizedSearchCV** with `StratifiedKFold` for hyperparameter tuning.

**Best parameters achieved:**
{
'clf__n_estimators': 297,
'clf__max_depth': 5,
'clf__learning_rate': 0.1158,
'clf__subsample': 0.6888,
'clf__colsample_bytree': 0.5002,
'clf__reg_alpha': 0.5107,
'clf__reg_lambda': 0.8348
}


### 5️⃣ Evaluation
Performance on the test set:

| Metric | Score |
|--------|-------|
| Accuracy | 0.9921 |
| F1 (macro avg) | 0.9876 |
| F1 (weighted avg) | 0.9921 |

**Confusion Matrix:**

| True \ Pred | Junior | Mid-level | Senior |
|-------------|--------|-----------|--------|
| Junior      | 91     | 0         | 0      |
| Mid-level   | 3      | 557       | 0      |
| Senior      | 0      | 3         | 106    |

### 6️⃣ Feature Importance (Top 10)
Top predictive features from the preprocessed pipeline:

| Feature Index | Importance |
|---------------|------------|
| 68            | 0.1474     |
| 8             | 0.0710     |
| 7             | 0.0610     |
| 40            | 0.0606     |
| 98            | 0.0580     |
| 93            | 0.0542     |
| 3             | 0.0506     |
| 53            | 0.0470     |
| 54            | 0.0377     |
| 45            | 0.0334     |

> **Note:** One-hot encoding expands categorical features, so indices correspond to the transformed feature order.

---

## 🧩 Prediction Example
The trained pipeline can predict a developer’s seniority based on new data.

**Example Output:**
Junior


---

## 📊 Visualizations
- **Precision, Recall, F1-score per Class** bar chart
- **Confusion Matrix** heatmap

These help verify model reliability and detect any class imbalance issues.

---

## 🧱 Tech Stack
- Python 3.12  
- pandas, numpy  
- scikit-learn  
- imblearn  
- xgboost  
- matplotlib, seaborn  
- joblib  

---

## 🚀 Key Takeaways
- Feature extraction from text titles is critical for distinguishing seniority.
- Combining BorderlineSMOTE with XGBoost yields high accuracy and robustness.
- The pipeline structure allows seamless deployment and future updates.
- Model generalizes well across different roles and industries.

---

## 📁 File Structure
├── developers.csv
├── seniority_model.py
├── best_seniority_pipeline.joblib
├── README.md
└── plots/
├── confusion_matrix.png
└── f1_scores.png

