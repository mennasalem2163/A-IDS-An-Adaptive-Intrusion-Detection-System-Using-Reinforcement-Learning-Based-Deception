# A-IDS: Adaptive Intrusion Detection and Response

A research prototype exploring network intrusion detection with machine learning and deep learning, alongside a Q-learning response policy that selects **Block**, **Alert**, or **Allow**.

## Related Publication

**A-IDS: An Adaptive Intrusion Detection System Using Reinforcement Learning-Based Deception**  
2025 1st Future International Conference on Artificial Intelligence and Cybersecurity (FICAC).

[Read the paper on IEEE Xplore](https://ieeexplore.ieee.org/document/11341837)  
DOI: `10.1109/FICAC65757.2025.11341837`

This repository contains an experimental notebook associated with the project. It is not a verified reproduction of every experiment in the published paper. The paper PDF is not included.

## Project Overview

The notebook explores two separate tasks:

- **Binary intrusion detection:** classify network traffic as normal or malicious using Logistic Regression, a feed-forward neural network, and 1D convolutional neural networks.
- **Attack classification and response:** classify ten traffic categories using Random Forest, then select a response through a tabular Q-learning policy.

An autoencoder experiment also explores reconstruction-based anomaly detection.

## Features

- UNSW-NB15 dataset exploration and visualizations
- Missing-value handling, outlier clipping, scaling, and categorical encoding
- Training and validation of several detection models
- CNN hyperparameter search with Keras Tuner
- Confusion matrices and training-history plots
- Q-table and reward visualizations
- Random Forest and Q-table serialization using pickle

## Repository Structure

```text
A-IDS/
    notebooks/
        Deep_Learning_in_Intrusion_Detection_System.ipynb
    data/
        README.md
    README.md
    requirements.txt
    .gitignore
```

## Dataset

The notebook expects these UNSW-NB15 CSV files:

- `UNSW_NB15_training-set.csv`
- `UNSW_NB15_testing-set.csv`

Dataset files are not bundled. Download them from the [UNSW-NB15 dataset page](https://research.unsw.edu.au/projects/unsw-nb15-dataset) and follow its usage terms. The notebook currently reads files from `/content/`, as used in Google Colab.

## Getting Started

### Google Colab

1. Open the notebook in Google Colab.
2. Upload both dataset CSV files to `/content/`.
3. Install dependencies in a notebook cell:

   ```python
   %pip install -r requirements.txt
   ```

   Upload `requirements.txt` to the Colab working directory first, or install the listed packages directly.
4. Run the cells in order. Deep learning and hyperparameter tuning can take considerable time; a GPU runtime may help.

### Local Jupyter

Use a Python environment compatible with the listed dependencies:

```bash
python -m venv .venv
pip install -r requirements.txt
jupyter lab
```

Activate the environment before installation. Open the notebook and replace every `/content/` dataset path with the path to your local CSV files.

## Saved Experimental Results

These values are recorded in the supplied notebook outputs. They have not been independently reproduced, and the evaluation limitations below apply.

| Model | Task | Saved accuracy |
|---|---|---:|
| Logistic Regression | Binary detection | 91.58% |
| Feed-forward neural network | Binary detection | 94.61% |
| CNN | Binary detection | 95.05% |
| Tuned CNN | Binary detection | 95.43% |
| Random Forest | Multiclass attack classification | 89.23% |

Binary and multiclass results measure different tasks and should not be compared directly. The notebook also prints 96.17% as combined system accuracy; its current implementation measures agreement with an action mapping for predicted classes, rather than end-to-end correctness against ground truth.

## Evaluation Limitations

This notebook is preserved as supplied. The following issues need correction and retraining before its metrics can be treated as validated benchmark results:

1. **Preprocessing before splitting:** the binary experiments combine the supplied training and test sets and fit preprocessing before a new random split. Fit preprocessing on training data only and retain an untouched test set.
2. **Ground-truth feature in Random Forest:** the multiclass section retains the binary `label` column as an input while predicting `attack_cat`. Remove both target columns from model inputs.
3. **Combined metric definition:** evaluate response choices against true attack categories and explicit acceptable actions, rather than only the predicted category.
4. **Rare attack performance:** the saved report shows zero recall for Backdoor and Worms. Include per-class recall, macro F1, and false-positive rates.
5. **Simulated policy environment:** Q-learning uses predefined rewards and randomly sampled states. It does not demonstrate adaptation to live attacker outcomes.
6. **Terminal Q-learning update:** each training episode ends after one action, but the update includes a future-state value. A terminal update should not bootstrap from that value.
7. **Identifier feature:** remove `id` from the binary model inputs.
8. **Autoencoder evaluation:** the anomaly threshold is chosen using test reconstruction errors. Select thresholds on validation data and evaluate against held-out labels.

The notebook does not include a Django application, a live honeypot service, GAN augmentation, SMOTE, XGBoost, or Optuna experiments. Those components should not be inferred from this repository.

## Technologies

Python, pandas, NumPy, scikit-learn, TensorFlow/Keras, Keras Tuner, Matplotlib, and Seaborn.

## Research Attribution

For the publication's author list and citation metadata, refer to the linked IEEE Xplore record. This notebook should be described as a research prototype; its saved outputs do not establish production readiness.
