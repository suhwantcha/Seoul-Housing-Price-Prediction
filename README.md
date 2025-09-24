# 🏠 Seoul Real Estate Price Prediction

## 1. Project Overview
This repository predicts **transaction prices (“물건금액”) for properties in Seoul** using official real estate data.  
We:
- Preprocess the training and test datasets separately,
- Train multiple machine learning models,
- Select the best-performing model,
- Output predictions for the test data in `submission.csv`.

---

## 2. Repository Structure

'''
├── data/
│   ├── seoul_real_estate.csv         # Original Seoul real estate transaction dataset
│   ├── submission.csv                # Test data with empty target column
├── preprocessing_estate.ipynb          # Preprocessing training data
├── preprocessing_submission.ipynb      # Preprocessing test/submission data
├── model.ipynb                         # Model training, validation, prediction
└── README.md
'''

---

## 3. Dataset
- **Source:** 2024 Seoul Real Estate Transaction Data (as of 2024.09.13)  
- **Train Set:** 64,016 rows × 21 columns  
- **Test (submission) Set:** 7,116 rows × 21 columns  
- **Target Variable:** `물건금액` (transaction price, in 10,000 KRW)

### Key Features
- `자치구명`: District name (강남구, 서초구, 마포구, …)  
- `건물면적`, `토지면적`: Building & land area (m²)  
- `층`: Floor number  
- `건축년도`: Year built  
- `건물용도`: Housing type (단독다가구, 아파트, 연립다세대, 오피스텔)  
- `권리구분`: Rights info (입주권, 분양권)  
- `신고구분`: Transaction type (중개거래, 직거래)

> **Note:** All data used here is publicly available from the Seoul Open Data platform.

---

## 4. Data Preprocessing
The notebooks `preprocessing_estate.ipynb` and `preprocessing_submission.ipynb` implement the following pipeline:

### 4.1 Missing Value Handling
- **Numeric columns**: filled with median or zero depending on domain knowledge  
- **Categorical columns**: filled with “Unknown” or most frequent category  

### 4.2 Feature Engineering
- **Building Age**: calculated from `건축년도`  
- **Contract Year/Month**: extracted from `계약일`  
- **Floor Category**: grouped `층` into low/mid/high ranges to reduce sparsity  

### 4.3 Encoding
- **Label Encoding** for ordinal categorical variables  
- **One-Hot Encoding** for nominal variables with a small number of categories (e.g., `건물용도`, `권리구분`)  

### 4.4 Outlier Handling
- Removed or capped extreme values for `건물면적` and `토지면적`  
- Checked distribution of `물건금액` and filtered obvious errors  

### 4.5 Target Transformation
- **Log transformation** applied to the target variable `물건금액` to stabilize variance and improve model performance  
- During prediction, the inverse transformation (exp) is applied to return to the original scale  

### 4.6 Consistency Between Train/Test
- Ensured the same encoding and feature engineering steps applied to both training and submission datasets  
- Saved transformers/encoders from train preprocessing and reused them for submission preprocessing  

---

## 5. Modeling
`model.ipynb` implements and compares multiple regression models:
- Random Forest Regressor  
- XGBoost Regressor  
- CatBoost Regressor  
- KNeighbors Regressor  

We used cross-validation and hyperparameter tuning.  
The model with the **lowest RMSE on the validation set** was selected as the final model.

---

## 6. Evaluation Metric
- **RMSE (Root Mean Squared Error)**  
  - Measures the difference between predicted and actual transaction prices.  
  - Lower RMSE means more accurate predictions.  
  - This was the official competition metric.

Example interpretation:  
If RMSE = 1,500 → on average the prediction is off by about **1,500 × 10,000 KRW = 15 million KRW**.

---

## 7. License
This project uses **publicly available data from the Seoul Open Data platform**.  
Please cite the original data source when using this repository.
