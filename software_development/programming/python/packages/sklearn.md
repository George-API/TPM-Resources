# Scikit-learn Quick Reference Guide

## Table of Contents

- [Rapid Lookup](#rapid-lookup)
- [Common Patterns](#common-patterns)

---

## Rapid Lookup

**Quick Syntax Reference**: `model.fit(X, y)` - Train | `model.predict(X)` - Predict | `model.score(X, y)` - Evaluate | `preprocessor.fit_transform(X)` - Fit and transform | `Pipeline([...])` - Chain steps | `train_test_split()` - Split | `cross_val_score()` - Cross-validate

### Data Splitting
- `X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)` - 80/20 split
- `X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, stratify=y)` - Stratified (classification)

### Scaling
- `scaler = StandardScaler()` - Z-score normalization
- `X_train_scaled = scaler.fit_transform(X_train)` - Fit and transform
- `X_test_scaled = scaler.transform(X_test)` - Transform test (use same scaler)
- `X_scaled = MinMaxScaler().fit_transform(X)` - Scale to [0, 1]

### Encoding
- `y_encoded = LabelEncoder().fit_transform(y)` - Integer encoding (ordinal)
- `X_encoded = OneHotEncoder(sparse_output=False, drop='first').fit_transform(X_cat)` - One-hot (nominal)

### Imputation
- `X_imputed = SimpleImputer(strategy='mean').fit_transform(X)` - Mean imputation
- `X_imputed = SimpleImputer(strategy='median').fit_transform(X)` - Median
- `X_imputed = SimpleImputer(strategy='most_frequent').fit_transform(X)` - Mode
- `X_imputed = KNNImputer(n_neighbors=5).fit_transform(X)` - KNN-based

### Feature Engineering
- `X_pca = PCA(n_components=2).fit_transform(X)` - Dimensionality reduction
- `explained_variance = PCA().fit(X).explained_variance_ratio_` - Variance explained

### Linear Models
- `lr = LinearRegression().fit(X_train, y_train)` - Linear regression
- `y_pred = lr.predict(X_test)` - Predict
- `score = lr.score(X_test, y_test)` - R² score
- `ridge = Ridge(alpha=1.0).fit(X_train, y_train)` - Ridge (L2)
- `lasso = Lasso(alpha=1.0).fit(X_train, y_train)` - Lasso (L1)

### Classification
- `logreg = LogisticRegression(random_state=42, max_iter=1000).fit(X_train, y_train)` - Logistic regression
- `y_pred = logreg.predict(X_test)` - Predict classes
- `y_proba = logreg.predict_proba(X_test)` - Predict probabilities
- `dt = DecisionTreeClassifier(max_depth=5, random_state=42).fit(X_train, y_train)` - Decision tree
- `rf = RandomForestClassifier(n_estimators=100, random_state=42).fit(X_train, y_train)` - Random forest
- `gb = GradientBoostingClassifier(n_estimators=100, random_state=42).fit(X_train, y_train)` - Gradient boosting
- `feature_importance = rf.feature_importances_` - Feature importance

### Regression
- `rf_reg = RandomForestRegressor(n_estimators=100, random_state=42).fit(X_train, y_train)` - Random forest regression

### Clustering
- `kmeans = KMeans(n_clusters=3, random_state=42, n_init=10).fit(X)` - K-means
- `labels = kmeans.predict(X)` - Cluster labels
- `labels = DBSCAN(eps=0.5, min_samples=5).fit_predict(X)` - DBSCAN

### Pipelines
- `pipeline = Pipeline([('scaler', StandardScaler()), ('model', LogisticRegression())])` - Chain steps
- `pipeline.fit(X_train, y_train)` - Fit
- `y_pred = pipeline.predict(X_test)` - Predict
- `pipeline = make_pipeline(StandardScaler(), LogisticRegression())` - Shorthand

### Column Transformer
- `preprocessor = ColumnTransformer([('num', StandardScaler(), ['age', 'income']), ('cat', OneHotEncoder(), ['category', 'region'])])` - Scale numeric + encode categorical
- `X_transformed = preprocessor.fit_transform(X)` - Transform mixed types

### Evaluation (Classification)
- `accuracy = accuracy_score(y_test, y_pred)` - Accuracy
- `precision = precision_score(y_test, y_pred)` - Precision
- `recall = recall_score(y_test, y_pred)` - Recall
- `f1 = f1_score(y_test, y_pred)` - F1 score
- `roc_auc = roc_auc_score(y_test, y_proba[:, 1])` - ROC AUC (binary)

### Evaluation (Regression)
- `mse = mean_squared_error(y_test, y_pred)` - MSE
- `rmse = np.sqrt(mean_squared_error(y_test, y_pred))` - RMSE
- `mae = mean_absolute_error(y_test, y_pred)` - MAE
- `r2 = r2_score(y_test, y_pred)` - R²

### Cross-Validation
- `scores = cross_val_score(model, X, y, cv=5, scoring='accuracy')` - 5-fold CV
- `scores = cross_val_score(model, X, y, cv=5, scoring='neg_mean_squared_error')` - CV with MSE

### Hyperparameter Tuning
- `param_grid = {'C': [0.1, 1, 10], 'penalty': ['l1', 'l2']}` - Parameter grid
- `grid = GridSearchCV(LogisticRegression(), param_grid, cv=5, scoring='accuracy')` - Grid search
- `grid.fit(X_train, y_train)` - Fit
- `best_model = grid.best_estimator_` - Best model
- `best_params = grid.best_params_` - Best params

---

## Common Patterns

### Preprocessing Pipeline (Numeric + Categorical)

```python
from sklearn.compose import ColumnTransformer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.impute import SimpleImputer

preprocessor = ColumnTransformer([
    ('num', Pipeline([
        ('imputer', SimpleImputer(strategy='median')),
        ('scaler', StandardScaler())
    ]), ['age', 'income']),
    ('cat', Pipeline([
        ('imputer', SimpleImputer(strategy='most_frequent')),
        ('encoder', OneHotEncoder(drop='first', sparse_output=False))
    ]), ['category', 'region'])
])
X_transformed = preprocessor.fit_transform(X_train)
```

### End-to-End ML Pipeline

```python
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42, stratify=y)

pipeline = Pipeline([
    ('scaler', StandardScaler()),
    ('model', RandomForestClassifier(n_estimators=100, random_state=42))
])
pipeline.fit(X_train, y_train)
y_pred = pipeline.predict(X_test)
```

### Hyperparameter Tuning

```python
from sklearn.model_selection import GridSearchCV

pipeline = Pipeline([
    ('scaler', StandardScaler()),
    ('model', RandomForestClassifier(random_state=42))
])

param_grid = {
    'model__n_estimators': [50, 100, 200],
    'model__max_depth': [5, 10, None],
    'model__min_samples_split': [2, 5, 10]
}

grid = GridSearchCV(pipeline, param_grid, cv=5, scoring='accuracy', n_jobs=-1)
grid.fit(X_train, y_train)
best_model = grid.best_estimator_
```

### Feature Selection

```python
from sklearn.feature_selection import SelectKBest, f_classif, RFE, SelectFromModel

# Univariate
selector = SelectKBest(score_func=f_classif, k=10)
X_selected = selector.fit_transform(X_train, y_train)

# Recursive Feature Elimination
rfe = RFE(estimator=RandomForestClassifier(), n_features_to_select=10)
X_selected = rfe.fit_transform(X_train, y_train)

# From model importance
selector = SelectFromModel(RandomForestClassifier(), threshold='median')
X_selected = selector.fit_transform(X_train, y_train)
```

### Model Persistence

```python
import joblib

joblib.dump(model, 'model.pkl')
joblib.dump(scaler, 'scaler.pkl')

model = joblib.load('model.pkl')
scaler = joblib.load('scaler.pkl')
y_pred = model.predict(scaler.transform(X_new))
```

### Probability Threshold Tuning

```python
from sklearn.metrics import precision_recall_curve

y_proba = model.predict_proba(X_test)[:, 1]
precision, recall, thresholds = precision_recall_curve(y_test, y_proba)
f1_scores = 2 * (precision * recall) / (precision + recall)
optimal_threshold = thresholds[np.argmax(f1_scores)]
y_pred = (y_proba >= optimal_threshold).astype(int)
```

### Handling Imbalanced Classes

```python
from sklearn.utils import class_weight
from imblearn.over_sampling import SMOTE
from imblearn.pipeline import Pipeline as ImbPipeline

# Class weights
class_weights = class_weight.compute_class_weight('balanced', classes=np.unique(y_train), y=y_train)
model = LogisticRegression(class_weight=dict(zip(np.unique(y_train), class_weights)))

# SMOTE
smote = SMOTE(random_state=42)
X_resampled, y_resampled = smote.fit_resample(X_train, y_train)

# Pipeline
pipeline = ImbPipeline([
    ('oversample', SMOTE(random_state=42)),
    ('model', RandomForestClassifier(random_state=42))
])
pipeline.fit(X_train, y_train)
```

