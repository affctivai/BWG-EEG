# BWGp-EEG
Experimental code for Bures–Wasserstein Geometry and Reference Estimation for EEG Artifact Detection.

This repository implements covariance-matrix-based artifact detection on the **TUAR** and **EEGEOG** datasets. It supports training and evaluation of Potato-based models under both **cross-subject** and **cross-session** settings.

<p class="callout info">A success message</p>
---

## Overview

This project converts EEG epochs into covariance matrices and classifies them as clean or artifact-contaminated using Riemannian Potato or Wasserstein Potato models.

The repository includes the following components:

- **DataLoader**
  - Loads TUAR and EEGEOG datasets
  - Constructs cross-subject and cross-session splits
  - Computes covariance matrices and manages cached data

- **Potato**
  - Basic Potato model
  - Potato model for epsilon-separation experiments
  - Potato model with cached covariance fitting

- **Experiment**
  - Connects data loaders with Potato models
  - Runs repeated experiments automatically
  - Saves confusion matrices and trained models

---

## Repository Structure

    .
    ├── DataLoader
    │   ├── ASemiSimulatedEEGEOGLoader.py
    │   ├── PARAMETERS.py
    │   ├── TUARLoader.py
    │   ├── __init__.py
    │   └── cache_control.py
    ├── Potato
    │   ├── PotatoLoader.py
    │   ├── __init__.py
    │   ├── potato.py
    │   ├── potato_cachedFitting.py
    │   ├── potato_epsilon.py
    │   └── utils.py
    ├── LICENSE
    ├── README.md
    └── basicExperiment.py

---

## Experiment Pipeline

1. Load the EEG dataset.
2. Convert each EEG epoch into a covariance matrix.
3. Construct the train/test split according to the experimental setting:
   - cross-subject
   - cross-session
4. Train a Potato model using clean covariance matrices from the training set.
5. Predict whether each test covariance matrix is clean or artifact-contaminated.
6. Save the confusion matrix as a CSV file. If needed, trained models and intermediate results are also saved as cached files.

---

## Data Loaders

### `DataLoader/TUARLoader.py`

Loader for constructing cross-subject and cross-session experiments on the TUAR dataset.

#### Main Functions

##### `get_cross_subject(subjectID: str)`

- **Input**
  - `subjectID`: test subject ID

- **Output**
  - `X_train`: clean covariance matrices from other subjects
  - `X_test`: covariance matrices from the target subject
  - `y_test`: labels for the target subject

##### `get_cross_session(subjectID: str, testSession: str)`

- **Input**
  - `subjectID`: target subject ID
  - `testSession`: target test session

- **Output**
  - `X_train`: training covariance matrices
  - `X_test`: test covariance matrices
  - `y_test`: test labels

---

### `DataLoader/ASemiSimulatedEEGEOGLoader.py`

Loader for constructing cross-subject and cross-session experiments on the Semi-Simulated EEGEOG dataset.

#### Main Functions

##### `get_cross_subject(subjectID: str)`

- **Input**
  - `subjectID`: test subject ID

- **Output**
  - `X_train`: clean covariance matrices from other subjects
  - `X_test`: covariance matrices from the target subject
  - `y_test`: labels for the target subject

##### `get_cross_session(subjectID: str, testSession: str)`

- **Input**
  - `subjectID`: target subject ID
  - `testSession`: target test session

- **Output**
  - `X_train`: training covariance matrices
  - `X_test`: test covariance matrices
  - `y_test`: test labels

---

## Potato Models

### `Potato/potato.py`

Basic Potato model.

#### Methods

- `fit(X)`
- `transform(X)`
- `predict(X)`
- `predict_proba(X)`

#### Options

- `metric`: `"riemann"` or `"wasserstein"`
- `center_method`: `"mean"` or `"median"`
- `threshold`
- Optimal transport parameters:
  - `ot_reg`
  - `ot_num_iter`
  - `ot_tol`

---

### `Potato/potato_epsilon.py`

Potato model for epsilon-separation experiments.

#### Epsilon Parameters

- `median_eps`
- `log_eps`
- `stats_eps`

---

### `Potato/potato_cachedFitting.py`

Potato model for cache-based fitting.

This version avoids loading all covariance matrices into memory at once and instead accesses them using indices.

Example:

```python
train_idx = np.arange(get_num_clean_covs())
model.fit(train_idx)
```

---

## Cache Utility

### `DataLoader/cache_control.py`

- `cacheExists(...)`
- `cacheSave(...)`

---
