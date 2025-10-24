---
layout: default
title: Sklearn Transformers and Estimators Part II | Jakub Smolik
---

[..](./index.md)

# Part II: Workflow Composition and Automation

Part II focuses on how to effectively compose and automate machine learning workflows using scikit-learn’s transformers, estimators, pipelines, and related tools. We cover practical techniques for building robust, reusable, and maintainable ML pipelines.

## Table of Contents

### [Chapter 4. The Transformer Chain: Pipelines](#chapter-4-the-transformer-chain-pipelines-1)

Learn how `Pipeline` objects organize preprocessing and modeling steps into a single composite estimator.

**4.1. Motivation**

- Data leakage prevention
- Encapsulation of workflow

**4.2. Pipeline Structure**

- Step tuples: (name, estimator)
- How `fit` and `predict` flow through steps

**4.3. Accessing Internal Steps**

- `named_steps` dictionary
- The double-underscore parameter access syntax

**4.4. Pipeline as Estimator**

- Pipelines are themselves estimators
- Nested transformations and predictions

**4.5. Helper: `make_pipeline()`**

- Auto-naming and when it’s useful

**4.6. Grid Search Integration**

- Using pipelines inside `GridSearchCV` and `cross_val_score`

**Code Lab:**
Build a numeric-scaling + logistic regression pipeline and tune it.

**Deep Dive:**
Call sequence of `fit`, `transform`, and `predict` inside a pipeline — with diagrams.

### [Chapter 5. The Column-wise View: ColumnTransformer](#chapter-5-the-column-wise-view-columntransformer-1)

Handle datasets with mixed types (numeric, categorical, text) by applying different transformers to each subset of columns.

**5.1. Mixed Data Problem**

- Why mixed features require specialized handling

**5.2. Column Specification**

- Integer indices, string names, callables

**5.3. Multiple Transformers**

- Parallel application of pipelines

**5.4. Remainder and Sparse Threshold**

- Handling unmentioned columns
- Combining sparse/dense results

**5.5. Extracting Feature Names**

- `get_feature_names_out()` walkthrough

**5.6. Integration with Pipelines**

- `Pipeline` containing a `ColumnTransformer`
- Nested parameter grid tuning

**Code Lab:**
Preprocess a dataset with numeric and categorical columns using nested pipelines.

**Deep Dive:**
`FeatureUnion` vs `ColumnTransformer`: difference in data flow logic.

---

## Chapter 4. The Transformer Chain: Pipelines

Chapters 2 and 3 established the unified API for **Transformers** and **Predictors**. While useful individually, these components are rarely used in isolation. The **`Pipeline`** object is scikit-learn's architectural solution for chaining these estimators into a single, cohesive workflow. The `Pipeline` is a **meta-estimator** that sequentially applies a list of transformers and, optionally, a final predictor.

Mastering the `Pipeline` is essential because it enforces experimental rigor, simplifies code, and enables seamless hyperparameter tuning across the entire machine learning process, from raw data conditioning to final prediction. This chapter will detail the structure, behavior, and powerful parameter management capabilities that make `Pipeline` the central element of complex scikit-learn workflows.

## Section 4.1. Motivation

In this section, we discuss the core reasons for using `Pipeline`, focusing on the critical issues of data leakage and workflow organization that it solves.

### Data leakage prevention

As discussed in Chapter 2, data preprocessing often requires calculating statistics (like mean or variance) on the training data (`fit`) and applying those statistics consistently to the test data (`transform`). If the test data is accidentally included in the calculation of these statistics, **data leakage** occurs, leading to an artificially optimistic assessment of model performance.

The `Pipeline` solves this problem by ensuring the **Fit-Once, Transform-Many** principle is adhered to across the entire workflow. When `pipeline.fit(X_train, y_train)` is called:

1.  The first step (a transformer) calls `fit_transform(X_train)`.
2.  The output is passed to the second step, which calls `fit_transform()`.
3.  This continues until the final step (the predictor), which calls `fit(X_transformed, y_train)`.

When `pipeline.predict(X_test)` is called, **all** intermediate steps automatically call only `transform(X_test)`, guaranteeing that the test data does not influence any of the fitted parameters.

### Encapsulation of workflow

A `Pipeline` encapsulates all data preparation and modeling logic into a single Python object. This leads to three key benefits:

1.  **Simplified API:** Instead of managing multiple separate objects (`scaler.fit()`, `scaler.transform()`, `model.fit()`, `model.predict()`), the entire process is managed via a single `pipeline.fit()` or `pipeline.predict()` call.
2.  **Ease of Persistence:** Instead of saving and loading five different objects, only the single, fitted `Pipeline` object needs to be persisted via `joblib.dump()`.
3.  **Seamless Tuning:** The unified object can be passed directly to meta-estimators like `GridSearchCV`, enabling the tuning of both preprocessing steps and model hyperparameters simultaneously.

## Section 4.2. Pipeline Structure

This section breaks down the technical structure of the `Pipeline` object, defining the step format and tracing how data and method calls flow through the chain.

### Step tuples: `(name, estimator)`

A `Pipeline` is instantiated using a list of steps, where each step is represented by a **tuple** of two elements:

1.  **`name` (string):** A unique, descriptive name for the step (e.g., `'scaling'`, `'impute'`, `'model'`). This name is crucial for parameter introspection and tuning.
2.  **`estimator` (object):** An instance of a scikit-learn estimator (transformer or predictor).

**Constraint:** All but the last step **must be transformers** (must implement `fit_transform`). The final step can be any type of estimator (transformer, predictor, or meta-estimator).

```python
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler
from sklearn.decomposition import PCA
from sklearn.linear_model import LogisticRegression

# Define the steps as a list of (name, estimator) tuples
steps = [
    ('scaler', StandardScaler()),      # Step 1: Transformer
    ('pca', PCA(n_components=10)),    # Step 2: Transformer
    ('clf', LogisticRegression())     # Step 3: Final Estimator (Predictor)
]

pipeline = Pipeline(steps)
print(f"Pipeline created with {len(pipeline.steps)} steps.")
```

### How `fit` and `predict` flow through steps

The core logic of the `Pipeline` is in its method dispatch, which is a crucial example of the composition pattern.

| Pipeline Method Called        | Execution Flow                                            | Data Flow                                                       |
| :---------------------------- | :-------------------------------------------------------- | :-------------------------------------------------------------- |
| **`pipeline.fit(X, y)`**      | For step $i=1$ to $N-1$: **`step_i.fit_transform(X_i)`**. | Output of step $i$ becomes input $X_{i+1}$ for step $i+1$.      |
|                               | For step $N$: **`step_N.fit(X_N, y)`**.                   | The final step fits the predictor on the fully transformed $X$. |
| **`pipeline.predict(X_new)`** | For step $i=1$ to $N-1$: **`step_i.transform(X_new_i)`**. | Output of step $i$ becomes input $X_{new, i+1}$ for step $i+1$. |
|                               | For step $N$: **`step_N.predict(X_N)`**.                  | The final step makes a prediction.                              |

**Deep Technical Dive:** The `fit` method of the `Pipeline` stores the transformed output _temporarily_ in memory before passing it to the next step. Importantly, the `Pipeline` itself inherits from `BaseEstimator` and becomes a **fitted estimator** only after the final step has successfully completed its fit.

## Section 4.3. Accessing Internal Steps

This section details the two standard ways to inspect and interact with the components _inside_ a fitted or unfitted `Pipeline`.

### `named_steps` dictionary

After instantiation, the `Pipeline` makes its component estimators accessible via the **`named_steps`** attribute, which is a standard Python dictionary mapping the step name to the estimator object.

This is the primary way to access the **fitted state** (the `_`-suffixed attributes) of internal components after `pipeline.fit()` is called.

```python
import numpy as np
X = np.array([[1, 2], [3, 4]])
y = np.array([0, 1])

pipeline.fit(X, y)

# Accessing the fitted StandardScaler object
scaler = pipeline.named_steps['scaler']

# Inspecting the learned state (mean_)
print(f"Scaler Mean: {scaler.mean_}")

# Accessing the fitted Logistic Regression coefficients
clf_coef = pipeline.named_steps['clf'].coef_
print(f"Classifier Coefficients: {clf_coef}")
```

### The double-underscore parameter access syntax

As introduced in Chapter 1, parameter introspection and modification are handled via the `get_params()` and `set_params()` methods, which rely on the **double-underscore (`__`)** convention for namespacing nested parameters.

The format is always `[step_name]__[parameter_name]`. This unified parameter space allows for dynamic modification of any setting within the workflow, which is foundational for hyperparameter tuning (Section 4.6).

```python
# Modifying parameters on the UN-FITTED pipeline
print("Original PCA components:", pipeline.get_params()['pca__n_components'])

# Use set_params to change two parameters simultaneously
pipeline.set_params(
    pca__n_components=2,           # Change PCA's hyperparameter
    clf__C=10.0                    # Change Logistic Regression's hyperparameter
)

print("New PCA components:", pipeline.get_params()['pca__n_components'])
print("New LogisticRegression C:", pipeline.get_params()['clf__C'])
```

## Section 4.4. Pipeline as Estimator

In this section, we solidify the concept that the `Pipeline` itself acts as a single, fully compliant scikit-learn estimator, capable of being chained or nested.

### Pipelines are themselves estimators

Because the `Pipeline` class inherits from `BaseEstimator`, `TransformerMixin` (if the final step is a transformer), and `ClassifierMixin`/`RegressorMixin` (if the final step is a predictor), the `Pipeline` object fully complies with the unified API.

- If the final step is a **Predictor**, the `Pipeline` implements `predict()`, `score()`, `predict_proba()`, etc., by delegating the call to the final step.
- If the final step is a **Transformer** (e.g., used for feature engineering), the `Pipeline` implements `transform()` and `fit_transform()`.

This design makes the `Pipeline` incredibly versatile; it can be treated as a black-box model suitable for deployment or as an intermediate feature engineering step in a larger meta-estimator.

### Nested transformations and predictions

The fact that `Pipeline` is an estimator allows for **nested structures**. For instance, one step in a parent pipeline could be another child pipeline, or a `ColumnTransformer` (Chapter 5) could contain multiple nested pipelines. This ability to compose estimators recursively is the true power of scikit-learn's architecture.

## Section 4.5. Helper: `make_pipeline()`

This section introduces a convenient factory function that simplifies pipeline creation in scenarios where step names aren't critical.

### Auto-naming and when it’s useful

Manually creating the `(name, estimator)` tuples can be tedious. The helper function **`sklearn.pipeline.make_pipeline`** simplifies this by automatically generating step names based on the class name of the estimator, converted to lowercase.

- `StandardScaler()` becomes `'standardscaler'`.
- `LogisticRegression(C=0.1)` becomes `'logisticregression'`.

**When to use `make_pipeline`:**

- For quick prototyping and simple workflows where you don't need highly descriptive step names.
- When the auto-generated names (e.g., `'standardscaler__C'`) are acceptable for parameter tuning.

**When to use `Pipeline` (manual):**

- When two estimators of the same class are used (e.g., two `PCA` steps); `make_pipeline` would fail to create unique names.
- When working with `ColumnTransformer` or complex logic where explicit, human-readable step names are crucial for debugging and maintenance.

```python
from sklearn.pipeline import make_pipeline
from sklearn.svm import SVC

# Create a pipeline using the helper function
auto_pipeline = make_pipeline(StandardScaler(), SVC(gamma='auto'))

print("Auto-generated step names:")
print(auto_pipeline.steps)

# Accessing parameters requires using the auto-generated names
auto_pipeline.set_params(svc__C=10.0)
print("\nParameter set using auto-name 'svc'.")
```

```
Auto-generated step names:
[('standardscaler', StandardScaler()), ('svc', SVC(gamma='auto'))]

Parameter set using auto-name 'svc'.
```

## Section 4.6. Grid Search Integration

This section explains how the `Pipeline` is the key to correct and robust hyperparameter optimization using cross-validation tools.

### Using pipelines inside `GridSearchCV` and `cross_val_score`

The `Pipeline` is the **only correct way** to combine preprocessing steps and a final model when using model selection tools like `GridSearchCV` or `cross_val_score`.

**Correctness:** When a `Pipeline` is passed to `GridSearchCV`, the following occurs for every parameter combination and cross-validation fold:

1.  `GridSearchCV` uses `clone()` to create a fresh, unfitted copy of the `Pipeline`.
2.  It uses `set_params()` on this copy to apply the current parameter settings (using the `__` convention).
3.  It calls `pipeline.fit(X_train_fold, y_train_fold)`.
4.  It calls `pipeline.score(X_val_fold, y_val_fold)`.

This ensures that the feature selection or scaling parameters are re-calculated from scratch **only on the training fold** for every iteration, perfectly preventing **data leakage into the cross-validation loop**.

```python
from sklearn.model_selection import GridSearchCV
from sklearn.preprocessing import StandardScaler
from sklearn.linear_model import LogisticRegression
from sklearn.datasets import make_classification
import numpy as np

X, y = make_classification(n_samples=100, n_features=10, random_state=42)

# Define the pipeline
pipeline_clf = Pipeline([
    ('scaler', StandardScaler()),
    ('logreg', LogisticRegression(random_state=42))
])

# Define the search space using the double-underscore convention
param_grid = {
    'scaler__with_mean': [True, False],  # Hyperparameter for the Transformer
    'logreg__C': [0.1, 1.0, 10.0]       # Hyperparameter for the Predictor
}

# Integrate the pipeline directly into GridSearchCV
grid_search = GridSearchCV(
    estimator=pipeline_clf,
    param_grid=param_grid,
    cv=3
)

# When fit is called, the pipeline handles the complex internal data flow and cloning
grid_search.fit(X, y)

print(f"Best parameter combination found: {grid_search.best_params_}")
print(f"Best cross-validation score: {grid_search.best_score_:.4f}")
```

```
Best parameter combination found: {'logreg__C': 1.0, 'scaler__with_mean': True}
Best cross-validation score: 0.9498
```

## Code Lab

### Build a numeric-scaling + logistic regression pipeline and tune it.

This lab demonstrates the full power of the `Pipeline` by integrating preprocessing, modeling, and parameter tuning into one object.

```python
from sklearn.datasets import load_diabetes
from sklearn.model_selection import GridSearchCV, train_test_split
from sklearn.preprocessing import StandardScaler, PolynomialFeatures
from sklearn.linear_model import Ridge
from sklearn.pipeline import Pipeline
import numpy as np

# 1. Load Data
X, y = load_diabetes(return_X_y=True)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=0)

# 2. Define the Pipeline
# We chain scaling, feature engineering, and a regressor
pipeline_reg = Pipeline([
    ('scaler', StandardScaler()),
    ('poly', PolynomialFeatures(include_bias=False)), # include_bias=False is a hyperparameter
    ('ridge', Ridge(random_state=0))
])

# 3. Define the Parameter Grid (using __ convention)
# We tune a transformer (PolynomialFeatures degree) and a predictor (Ridge alpha)
param_grid = {
    'poly__degree': [1, 2, 3],        # Linear, Quadratic, Cubic terms
    'ridge__alpha': [0.1, 1.0, 10.0]  # Regularization strength
}

# 4. Integrate with GridSearchCV
search = GridSearchCV(
    estimator=pipeline_reg,
    param_grid=param_grid,
    cv=5,                             # Use 5-fold cross-validation
    scoring='neg_mean_squared_error',
    n_jobs=-1
)

# 5. Fit the entire tuning process on training data
search.fit(X_train, y_train)

# 6. Inspect Results
print("\n--- Tuning Results ---")
print(f"Best Parameters: {search.best_params_}")
print(f"Best CV Score (Negative MSE): {search.best_score_:.4f}")

# The entire best model is stored in best_estimator_
best_pipeline = search.best_estimator_
test_score = best_pipeline.score(X_test, y_test)
print(f"Test Set R^2 Score using best pipeline: {test_score:.4f}")

# Accessing parameters of the best model's final step
best_alpha = best_pipeline.named_steps['ridge'].alpha
print(f"Alpha of final fitted model: {best_alpha}")
```

```
--- Tuning Results ---
Best Parameters: {'poly__degree': 1, 'ridge__alpha': 10.0}
Best CV Score (Negative MSE): -3006.2306
Test Set R^2 Score using best pipeline: 0.3925
Alpha of final fitted model: 10.0
```

## Deep Dive

### Call sequence of `fit`, `transform`, and `predict` inside a pipeline — with diagrams.

The strength of the `Pipeline` lies in its ability to delegate method calls sequentially, handling the data flow internally. Understanding this call sequence is essential for debugging.

**Conceptual Flow of `pipeline.fit(X, y)`:**
The `Pipeline` iterates through its steps:

1.  **Step 1 (Transformer):** `step_1.fit_transform(X, y)` is called. It learns its state (e.g., mean/scale) and returns $X_1$.
2.  **Step 2 (Transformer):** `step_2.fit_transform(X_1, y)` is called. It learns its state from $X_1$ and returns $X_2$.
3.  **...Intermediate Steps:** This process continues until $X_{N-1}$ is generated.
4.  **Final Step (Predictor $N$):** `step_N.fit(X_{N-1}, y)` is called. It learns the model parameters (e.g., `coef_`).

**Conceptual Flow of `pipeline.predict(X_new)` (on unseen data):**

1.  **Step 1 (Transformer):** `step_1.transform(X_{new})` is called. It uses the state learned during the initial `fit()` (Chapter 2) and returns $X_{new, 1}$.
2.  **Step 2 (Transformer):** `step_2.transform(X_{new, 1})` is called. It uses its fitted state and returns $X_{new, 2}$.
3.  **...Intermediate Steps:** This process continues until $X_{new, N-1}$ is generated.
4.  **Final Step (Predictor $N$):** `step_N.predict(X_{new, N-1})` is called, producing the final output.

**Internal Implementation Detail:** To manage the flow of data $X_i$ to $X_{i+1}$, the `Pipeline` relies on the `_fit_transform_one` and `_transform_one` helper methods. These methods efficiently handle the data transfer and enforce the `fit_transform` or `transform` contract at each stage. The use of `fit_transform` for the intermediate steps during the initial `fit` is a conscious performance optimization; it prevents two passes over the data for each step.

## Key Takeaways

The reader should now understand:

- The **`Pipeline`** is the standard for combining transformers and predictors into a single, cohesive, and persistent object.
- It automatically prevents **data leakage** by enforcing the `fit_transform` / `transform` separation across all steps.
- Internal steps are accessed via **`named_steps`** after fitting, and parameters are managed using the **double-underscore (`__`)** syntax.
- `Pipeline` is a fully compliant estimator, allowing it to be integrated directly into model selection tools like **`GridSearchCV`**.
- The call sequence within the pipeline is a chain of `fit_transform` calls followed by a single final `fit`, and a chain of `transform` calls followed by a final `predict`.

The next logical step is to handle data with mixed feature types. **Chapter 5. The Column-wise View: ColumnTransformer** will address how to apply multiple, distinct pipelines simultaneously to different subsets of features.

---

## Chapter 5. The Column-wise View: ColumnTransformer

Real-world datasets are rarely homogeneous. They typically consist of a mix of feature types: numeric, categorical, date/time, and text. Each type requires a different set of preprocessing steps—for instance, numeric columns need scaling, while categorical columns need encoding. The **`ColumnTransformer`** is scikit-learn's powerful meta-estimator designed to solve this **mixed data problem**.

The `ColumnTransformer` allows you to apply different sets of transformations (which can themselves be complex `Pipeline` objects) to different subsets of columns **in parallel**, before concatenating the results into a single feature matrix suitable for a final model. This modular approach is essential for building robust, transparent, and maintainable end-to-end machine learning workflows.

## Section 5.1. Mixed Data Problem

In this section, we explain why heterogeneous data structures necessitate specialized tools like `ColumnTransformer` for effective and safe preprocessing.

### Why mixed features require specialized handling

Standard scikit-learn transformers, such as `StandardScaler` or `OneHotEncoder`, are designed to process a single type of data.

- **Failure of Simple Approach:** If you apply a `StandardScaler` (which relies on calculating means and standard deviations) to a column of categorical strings, the operation will fail or produce meaningless numeric results. Similarly, applying an `OrdinalEncoder` to continuous numeric columns is incorrect.
- **The Need for Selectivity:** Because different features require different preparation, the overall preprocessing strategy must be selective. We need a mechanism to explicitly route specific columns to specific transformers while leaving others untouched or dropping them entirely.

The `ColumnTransformer` provides this routing mechanism. By specifying which columns (by index or name) go to which transformer, it ensures the correct operation is applied to the correct data type, maintaining the integrity and quality of the feature set before it reaches the final model.

## Section 5.2. Column Specification

We detail the flexible methods `ColumnTransformer` provides for selecting which columns should be routed to a specific transformer.

### Integer indices, string names, callables

The `ColumnTransformer` is instantiated with a list of transformers, where each entry is a tuple: `(name, transformer_instance, columns)`. The final element, `columns`, specifies the feature subset and is highly flexible:

1.  **Integer Indices:** `[0, 2, 5]` selects the 1st, 3rd, and 6th columns of the input data $X$. This is simplest but **fragile** if the input data column order changes.
2.  **String Names:** `['age', 'fare']` selects columns by their label. This is the **most robust** method when input $X$ is a pandas DataFrame (the recommended input format when using names).
3.  **Boolean Masks:** A NumPy array or list of `True`/`False` values matching the number of features.
4.  **Slices/Ranges:** `slice(2, 5)` selects a range of indices.
5.  **Callables (Advanced):** A function that takes the array/DataFrame and returns the selected columns. This is useful for dynamic selection, such as selecting all columns of a certain `dtype`.

**Design Insight:** The ability to use string names is why `ColumnTransformer` often requires input data $X$ to be a pandas DataFrame, or an object supporting string indexing, although it can accept NumPy arrays if only integer indices are used.

```python
from sklearn.compose import ColumnTransformer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.pipeline import Pipeline
import pandas as pd
import numpy as np

# Sample mixed data (requires pandas for string names)
data = pd.DataFrame({
    'age': [20, 30, 40],
    'city': ['NY', 'SF', 'NY'],
    'fare': [10.5, 20.1, 5.0],
    'ID': [1, 2, 3] # Column to ignore
})

# Define the list of transformations (the transformers argument)
preprocessor = ColumnTransformer(
    transformers=[
        # Transformer 1: Scale numeric columns by string name
        ('num', StandardScaler(), ['age', 'fare']),

        # Transformer 2: One-hot encode categorical columns by string name
        ('cat', OneHotEncoder(handle_unknown='ignore'), ['city'])
    ],
    remainder='drop' # Explicitly drop 'ID' column
)

# Fitting the preprocessor
X_transformed = preprocessor.fit_transform(data)
print(f"Original shape: {data.shape}")
print(f"Transformed shape: {X_transformed.shape}")
# (3 samples, 2 numeric + 2 city categories = 4 features)
print(data)
print(X_transformed)
```

```
Original shape: (3, 4)
Transformed shape: (3, 4)
   age city  fare  ID
0   20   NY  10.5   1
1   30   SF  20.1   2
2   40   NY   5.0   3

[[-1.22474487 -0.21902284  1.          0.        ]
 [ 0.          1.31947908  0.          1.        ]
 [ 1.22474487 -1.10045624  1.          0.        ]]
```

Note that the two numerical column are before the categorical ones, even though they were mixed in the original data. The transformed column order is determined by the order of the transformers.

## Section 5.3. Multiple Transformers

We clarify how the `ColumnTransformer` executes its role by applying transformations in parallel and then concatenating the results.

### Parallel application of pipelines

The core function of the `ColumnTransformer` is **parallel processing**. Unlike a standard `Pipeline` (Chapter 4) which processes data sequentially, the `ColumnTransformer` does the following when `fit_transform` is called:

1.  **Splitting:** The input $X$ is split into several data views based on the `columns` specified in each transformer tuple.
2.  **Fitting (Parallel):** Each transformer in the list is independently fitted and transformed on its respective subset of the training data. Critically, these operations do not interfere with each other.
3.  **Concatenation:** The results from all transformer applications (which are typically 2D NumPy arrays or sparse matrices) are horizontally concatenated (`numpy.hstack` or similar internal logic) to form the final, processed feature matrix.

**Best Practice: Nested Pipelines:** It's common and highly recommended for the **transformer instance** in the `ColumnTransformer` tuple to be a complete `Pipeline` itself. This allows you to apply a sequence of steps (e.g., `SimpleImputer` $\rightarrow$ `StandardScaler`) to the numeric columns and a different sequence to the categorical columns simultaneously.

```python
from sklearn.impute import SimpleImputer

# Numeric preprocessing pipeline
numeric_pipeline = Pipeline([
    ('imputer', SimpleImputer(strategy='mean')),
    ('scaler', StandardScaler())
])

# Categorical preprocessing pipeline
categorical_pipeline = Pipeline([
    ('imputer', SimpleImputer(strategy='most_frequent')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))
])

# Combining them in the ColumnTransformer
complex_preprocessor = ColumnTransformer(
    transformers=[
        ('num_pipe', numeric_pipeline, ['age', 'fare']),
        ('cat_pipe', categorical_pipeline, ['city'])
    ],
    remainder='drop'
)
# This design is highly modular and easy to extend.
```

## Section 5.4. Remainder and Sparse Threshold

This section covers the `ColumnTransformer`'s mechanisms for handling features not explicitly mentioned and how it manages mixed sparse and dense data.

### Handling unmentioned columns

The **`remainder`** parameter controls what happens to columns that are _not_ included in any of the explicitly defined `transformers` lists.

- **`remainder='drop'` (Default):** Any unmentioned columns are discarded from the output feature matrix. (Used in the example above to discard `'ID'`.)
- **`remainder='passthrough'`:** Any unmentioned columns are kept as-is and appended to the final feature matrix without any transformation. This is useful when some columns are already clean (e.g., binary flags).
- **`remainder=transformer_instance`:** A specific transformer (e.g., `StandardScaler()`) is applied to all unmentioned columns.

### Combining sparse/dense results

The `ColumnTransformer` handles the concatenation of feature blocks, which may include a mix of dense NumPy arrays and sparse SciPy matrices.

- **`sparse_threshold`:** This hyperparameter controls how the final output is managed. If the proportion of sparse matrices in the combined output exceeds this threshold (default $0.3$), the entire output is returned as a sparse matrix (using the most efficient common format, usually CSR). Otherwise, the sparse components are converted to dense, and the entire output is returned as a dense NumPy array.
- **Best Practice:** If you know your final model supports sparse input (e.g., `LogisticRegression`), you should ensure the `sparse_threshold` is set high (e.g., $1.0$) to avoid unnecessary memory conversion to dense format. If the final model requires dense input (e.g., certain kernel SVMs), you must ensure the output is dense.

## Section 5.5. Extracting Feature Names

We walk through the use of `get_feature_names_out()`, a recent but essential addition for mapping model coefficients back to human-readable features after complex transformations.

### `get_feature_names_out()` walkthrough

A significant challenge with complex pipelines is that transformations like `OneHotEncoder` or `PolynomialFeatures` change the number and meaning of the original features. After processing, the model sees features like `cat__city_SF` or `poly__fare^2`, not the original names.

The method **`get_feature_names_out(input_features=None)`** (available on `ColumnTransformer` and `Pipeline`) solves this. It traces the feature names through the entire transformation process and returns the final list of column names used by the predictor.

- **Naming Convention:** The output names are generated using the `__` (double-underscore) convention: `[transformer_name]__[original_feature_name]_[category_value]` (for encoding) or `[transformer_name]__[original_feature_name]` (for scaling).

This method is crucial for **model interpretation** (Chapter 10), allowing you to link the learned model parameters (e.g., `coef_` or `feature_importances_`) back to the features they represent.

```python
# Assuming 'complex_preprocessor' from 5.3 is fitted
complex_preprocessor.fit(data)

# Extracting the final feature names
feature_names = complex_preprocessor.get_feature_names_out()

print("Original Features: age, fare, city, ID (dropped)")
print("\nFinal Transformed Feature Names:")
print(feature_names)

# Expected output structure:
# ['num_pipe__age', 'num_pipe__fare', 'cat_pipe__city_NY', 'cat_pipe__city_SF']
```

## Section 5.6. Integration with Pipelines

This section demonstrates the standard, powerful structure of embedding a `ColumnTransformer` inside a primary `Pipeline`.

### `Pipeline` containing a `ColumnTransformer`

The most common and robust scikit-learn workflow structure involves a single, outer `Pipeline` where the first step is the `ColumnTransformer`, followed by a feature selector or the final predictor.

$$
\text{Workflow} = \text{Pipeline}(\text{ColumnTransformer} \rightarrow \text{Predictor})
$$

The `ColumnTransformer` acts as a large, complex **Transformer** step at the beginning of the `Pipeline`. This nested structure simplifies the API, as the entire workflow is now encapsulated in one object ready for deployment or tuning.

### Nested parameter grid tuning

When the `ColumnTransformer` is inside a `Pipeline`, we leverage the `__` (double-underscore) syntax multiple times to reach parameters nested deep within the structure.

**Parameter Path Example:**

1.  Assume a parent `Pipeline` step name: `'preprocessor'`.
2.  Assume the `ColumnTransformer` step name inside it: `'cat_pipe'`.
3.  Assume the `OneHotEncoder` step name inside the _categorical pipeline_: `'onehot'`.
4.  Assume the hyperparameter: `handle_unknown`.

The full parameter path for tuning becomes:
`preprocessor__cat_pipe__onehot__handle_unknown`

```python
# Full workflow setup
full_pipeline = Pipeline([
    ('preprocessor', complex_preprocessor), # The ColumnTransformer object
    ('clf', LogisticRegression(random_state=42))
])

# Define a tuning grid for nested components
tuning_grid = {
    # Tuning the numeric scaling strategy inside the ColumnTransformer
    'preprocessor__num_pipe__imputer__strategy': ['mean', 'median'],

    # Tuning the final model's hyperparameter
    'clf__C': [0.1, 1.0, 10.0]
}
```

## Code Lab

### Preprocess a dataset with numeric and categorical columns using nested pipelines.

This lab builds the canonical workflow structure and demonstrates how the `ColumnTransformer` handles the parallel data flow.

```python
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LogisticRegression
from sklearn.preprocessing import OneHotEncoder, StandardScaler
from sklearn.impute import SimpleImputer
from sklearn.compose import ColumnTransformer
from sklearn.pipeline import Pipeline
import pandas as pd

# 1. Create Sample Mixed Data
data = pd.DataFrame({
    'Age': [30, 45, np.nan, 22, 60],
    'Fare': [100.0, 50.0, 10.0, 15.0, np.nan],
    'Gender': ['male', 'female', 'male', 'male', 'female'],
    'Embarked': ['S', 'C', 'S', 'Q', 'S'],
    'Survived': [0, 1, 0, 1, 0]
})

X = data.drop('Survived', axis=1)
y = data['Survived']
X_train, X_test, y_train, y_test = train_test_split(X, y, random_state=42)

# 2. Define Sub-Pipelines
numeric_features = ['Age', 'Fare']
categorical_features = ['Gender', 'Embarked']

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())
])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='most_frequent')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))
])

# 3. Define the ColumnTransformer (The heart of the preprocessing)
preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)
    ],
    remainder='drop'
)

# 4. Define the Final Pipeline
clf_pipeline = Pipeline(steps=[
    ('preprocessor', preprocessor), # ColumnTransformer step
    ('classifier', LogisticRegression(solver='liblinear', random_state=42))
])

# 5. Fit the entire workflow (preprocessor and classifier)
clf_pipeline.fit(X_train, y_train)

# 6. Evaluate and Inspect
train_score = clf_pipeline.score(X_train, y_train)
test_score = clf_pipeline.score(X_test, y_test)

print(f"Training Accuracy: {train_score:.4f}")
print(f"Test Accuracy: {test_score:.4f}")

# Inspecting a deep parameter using the nested __ convention:
gender_category = clf_pipeline.named_steps['preprocessor'].named_transformers_['cat'].named_steps['onehot'].categories_[0]
print(f"\nLearned Gender Categories: {gender_category}")
```

## Deep Dive

### `FeatureUnion` vs `ColumnTransformer`: difference in data flow logic.

Before `ColumnTransformer` was introduced, the `sklearn.pipeline.FeatureUnion` was the primary method for combining features from multiple transformers. While both achieve parallel transformation and feature concatenation, their fundamental design and constraints differ significantly.

| Feature             | `FeatureUnion`                                                                                        | `ColumnTransformer`                                                         |
| :------------------ | :---------------------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------- |
| **Input Selection** | None. Assumes **all** transformers should run on the **entire** input $X$.                            | **Explicit**. Each transformer is directed to a specific subset of columns. |
| **Data Integrity**  | Prone to data leakage and error if transformers are sensitive to column order/type.                   | **Robust**. Solves the mixed data problem by routing only relevant columns. |
| **Remainder**       | No concept of unmentioned columns.                                                                    | Explicit `remainder` parameter (`'drop'`, `'passthrough'`, or transformer). |
| **Usability**       | Requires external column selection prior to use (e.g., custom transformers that only select indices). | Handles column selection internally by string name or index.                |

**Data Flow Logic Difference:**

- **`FeatureUnion`:** When `fit_transform(X)` is called, it passes the _full_ matrix $X$ to every constituent transformer. It relies on those transformers to _only_ operate on the relevant features and pass the rest through or ignore them, a method which is difficult to enforce and error-prone.
- **`ColumnTransformer`:** When `fit_transform(X)` is called, it first **splits** $X$ column-wise. It passes only the **relevant subset** $X_{sub}$ to each transformer. The transformer receives only the data it needs, simplifying the internal logic of the transformer and enforcing correctness.

The `ColumnTransformer` is a direct evolution of the `FeatureUnion` concept, providing a much safer, more explicit, and robust solution for handling heterogeneous data, making `FeatureUnion` mostly deprecated for modern workflows involving mixed column types.

## Key Takeaways

The reader should now understand:

- The **`ColumnTransformer`** is essential for handling real-world mixed data by applying different pipelines to different subsets of features **in parallel**.
- Column selection can be done robustly using **string names** (when using pandas) or integer indices.
- The `ColumnTransformer` uses the **`remainder`** parameter to control unmentioned columns and the **`sparse_threshold`** to manage output data type.
- The most powerful workflow involves nesting the `ColumnTransformer` within an outer **`Pipeline`**.
- **Nested parameter tuning** utilizes extended `__` syntax (e.g., `preprocessor__num__scaler__with_mean`).

With a robust mechanism for data preparation now fully established, the guide moves to the science of optimization. **Chapter 6. Model Selection and Parameter Tuning** will detail how to efficiently and correctly search the parameter space of these complex pipelines.
