# Optima Vision

## Clothing-Item Recognition Using Classical Machine Learning

**Course:** Applied Machine Learning (CSAI2017P)  
**Theme:** P06 - Clothing-item Recognition with Classical Machine Learning  
**Group:** Optima Vision  
**Institution:** UPES, Dehradun  
**Academic Year:** 2026-27

---

## Team

| Member | Enrolment Number | GitHub Username |
|---|---:|---|
| Ansh Monga | 590025575 | Ansham382 |
| Raunak Singh Negi | 590022626 | Raunak-Singh-Negi |
| Prince Sai | 590022756 | TODO |
| Anirudh Awasthi | 590027460 | Anirudhcode111 |

---

## Project Question

How far can classical, non-deep machine-learning methods classify
28x28 Fashion-MNIST clothing images after dimensionality reduction,
and which clothing classes are most difficult to distinguish?

Optima Vision compares K-Nearest Neighbours, RBF Support Vector
Machines, Random Forests and a small Multilayer Perceptron using
the same PCA representation and official Fashion-MNIST test split.

---

## Dataset

**Dataset:** Fashion-MNIST  
**Source:** Zalando Research  
**Licence:** MIT  
**Images:** 70,000 grayscale images  
**Resolution:** 28x28  
**Classes:** 10  
**Official training split:** 60,000  
**Official test split:** 10,000

Source:

https://github.com/zalandoresearch/fashion-mnist

### Download

Download these four original IDX gzip files using a web browser:

- `train-images-idx3-ubyte.gz`
- `train-labels-idx1-ubyte.gz`
- `t10k-images-idx3-ubyte.gz`
- `t10k-labels-idx1-ubyte.gz`

Place all four files, without extracting them, inside:

`data/raw/`

The original gzip files are committed to this repository.

---

## Method

The project implements its own IDX loader and preserves the official
60,000/10,000 Fashion-MNIST train-test split.

The training data is flattened from 28x28 images into 784 pixel
features and normalized from [0,255] to [0,1]. The official training
set is further divided into 48,000 model-fitting and 12,000
validation samples using a stratified split with random seed 42.

PCA is fitted exclusively on the model-fitting subset. A 95%
cumulative explained-variance criterion selected 187 principal
components, reducing the original 784 features by approximately
76%.

Hyperparameters are selected using validation data. The official
test set is not used for hyperparameter selection.

---

## Models

### Checkpoint 1 Baseline

Logistic Regression was used as the initial linear baseline.

- PCA components: 187
- Official test accuracy: **84.51%**
- Fit time: **42.8113 s**
- Test prediction time: **0.0064 s**

### Selected Classifiers

- KNN: `k=5`
- RBF SVM: `C=10`, `gamma=scale`
- Random Forest: `n_estimators=200`, `max_depth=40`
- MLP: hidden layers `(128,64)`, `tanh`, `max_iter=200`

The SVM parameter search used a stratified 15,000-example subset of
the model-fitting data for computational efficiency. The selected
configuration was subsequently trained using the full fitting set.

The initial MLP experiments used `max_iter=100` and reached the
iteration limit before satisfying the convergence criterion. The
maximum was increased to 200. In the convergence-check run, training
converged naturally after 96 iterations while retaining 89.375%
validation accuracy.

---

## Results

| Model | Test Accuracy | Macro-F1 | Fit Time (s) | Predict Time (s) |
|---|---:|---:|---:|---:|
| RBF SVM | **89.91%** | **89.90%** | 58.066 | 20.218 |
| MLP | 87.58% | 87.59% | 26.378 | 0.013 |
| KNN | 85.79% | 85.74% | 0.030 | 1.068 |
| Random Forest | 85.75% | 85.55% | 15.526 | 0.092 |

RBF SVM achieved the strongest predictive performance, although its
prediction time was substantially higher than the other models.

### Error Analysis

The SVM's main weakness is the **Shirt** class:

- Precision: **74.61%**
- Recall: **71.70%**
- F1: **73.13%**

Major confusions include:

- Shirt -> T-shirt/top: 117
- T-shirt/top -> Shirt: 98
- Coat -> Pullover: 89
- Shirt -> Pullover: 79
- Pullover -> Coat: 68
- Pullover -> Shirt: 65

The errors are concentrated among visually similar upper-body
garments. Distinctive classes such as Trouser, Sandal and Bag are
classified substantially more reliably.

---

## Project Structure

```text
.
|-- README.md
|-- requirements.txt
|-- REPRODUCIBILITY.md
|-- data/
|   |-- raw/
|   `-- processed/
|-- notebooks/
|   |-- 01-eda.ipynb
|   |-- 02-preprocessing.ipynb
|   |-- 03-models.ipynb
|   `-- 04-evaluation.ipynb
|-- src/
|   |-- data.py
|   |-- preprocessing.py
|   |-- models.py
|   `-- evaluation.py
|-- results/
|   |-- figures/
|   |-- tuning/
|   |-- metrics.csv
|   |-- classification_report.csv
|   `-- pca_sweep.csv
`-- report/
    `-- report.pdf

    ---

## Running the Project

The project was developed using Python in a virtual environment.
All required package versions are provided in `requirements.txt`.

From the repository root, create a virtual environment:

```powershell
python -m venv .venv
```

Activate it on Windows PowerShell:

```powershell
.\.venv\Scripts\Activate.ps1
```

Install the required dependencies:

```powershell
python -m pip install -r requirements.txt
```

Ensure the four Fashion-MNIST IDX gzip files are present in
`data/raw/`:

- `train-images-idx3-ubyte.gz`
- `train-labels-idx1-ubyte.gz`
- `t10k-images-idx3-ubyte.gz`
- `t10k-labels-idx1-ubyte.gz`

Start JupyterLab:

```powershell
jupyter lab
```

Run the notebooks from top to bottom in the following order:

1. `notebooks/01-eda.ipynb`
2. `notebooks/02-preprocessing.ipynb`
3. `notebooks/03-models.ipynb`
4. `notebooks/04-evaluation.ipynb`

See `REPRODUCIBILITY.md` for the complete experimental configuration,
random seeds, model settings, hardware information and reproducibility
notes.
