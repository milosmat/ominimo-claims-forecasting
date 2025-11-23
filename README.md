## Project structure and execution order

The repository is organised around four main notebooks:

1. `notebooks/relevant_notebooks/preCleaning_eda.ipynb`  
   - Exploratory data analysis on the original `freMTPL2freq.csv` file.  
   - Checks basic structure, missing values, zeroes, distributions of key variables,  
     and defines candidate bands (DrivAge, VehAge, BonusMalus, VehPower, Density).

2. `notebooks/relevant_notebooks/data_preparation.ipynb`  
   - Applies minimal structural cleaning (IDpol type, exposure > 0, no negative ClaimNb).  
   - Engineers the features used in all models (ClaimFreq, DrivAgeBand, VehAgeBand, BonusMalusBand, logDensity).  
   - Lumps rare `VehBrand` and `Region` levels into stable `..._lumped` categories.  
   - Exports the final modelling table as `data/claims_prepared.csv`.

3. `notebooks/relevant_notebooks/tweedie_glm_claims.ipynb`  
   - Builds Poisson and Tweedie GLM frequency models on `claims_prepared.csv`.  
   - Uses exposure as a weight, fits a baseline Poisson GLM and a tuned version.  
   - Analyses coefficients and aggregates them back to base features to get business-oriented feature importance.  
   - Runs feature ablation to simplify the GLM (dropping low-impact features)  
     and reports final GLM performance and error analysis (including top-10 residuals).

4. `notebooks/relevant_notebooks/xgboost_claims.ipynb`  
   - Trains tuned Poisson and Tweedie XGBoost models on `claims_prepared.csv`.  
   - Uses the same core feature set as the GLM (with some continuous variables instead of bands).  
   - Evaluates models with Poisson deviance, RMSE, exact match rate,  
     zero/non-zero behaviour and KL divergence on the {0,1,2,3+} distribution.  
   - Aggregates tree-based feature importances back to original features.  
   - Runs feature ablation experiments (e.g. removing `Area` or `logDensity`)  
     and trains a final Poisson model without `Area`, including top-10 error diagnostics.

## How to run

1. Place the original benchmark file `freMTPL2freq.csv` in the `data/` folder.
2. Create and activate a virtual environment (optional but recommended), then install dependencies:

```bash
python -m venv .venv
.venv\Scripts\activate  # Windows
pip install -r requirements.txt
