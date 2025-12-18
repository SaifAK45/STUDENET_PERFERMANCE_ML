## END TO END MACHINE LEARNING PROJECT
# Student Performance Prediction – End‑to‑End ML Project

This project is an end‑to‑end machine learning pipeline that predicts students’ **math scores** based on their demographics, parental background, and other academic factors. It covers everything from data ingestion and transformation, to model training and hyperparameter tuning, and finally serving predictions through a **Flask web application**.

## Project Overview

The main goal is to build a robust regression model that can estimate a student’s math score using features such as gender, race/ethnicity, parental education, lunch type, test preparation course, and reading/writing scores. The project is structured in a production‑style way with separate modules for each pipeline stage and reusable utilities.[^1][^2]

## Tech Stack

- Python
- Flask (for the web app)[^3]
- scikit‑learn (preprocessing, models, pipelines)[^2][^1]
- XGBoost (XGBRegressor)[^2]
- NumPy, Pandas[^1]


## Dataset

The project uses a student performance dataset stored as CSV files:

- `data.csv`: Raw dataset.[^4]
- `train.csv`, `test.csv`: Training and testing splits used in the pipeline.[^5][^6]

Key columns include:[^1]

- Categorical:
    - `gender`
    - `race_ethnicity`
    - `parental_level_of_education`
    - `lunch`
    - `test_preparation_course`
- Numerical:
    - `reading_score`
    - `writing_score`
- Target:
    - `math_score`


## Pipeline Architecture

The ML pipeline is modular and organized under `src`‑style components (as referenced in your imports).[^2][^1]

### 1. Data Ingestion

- File: `data_ingestion.py`[^7]
- Responsibilities:
    - Read the raw CSV data.
    - Split into train and test sets (`train.csv`, `test.csv`).
    - Save the processed files under an `artifacts` directory (path inferred from other modules).


### 2. Data Transformation

- File: `data_transformation.py`[^1]
- Responsibilities:
    - Define preprocessing pipelines for numerical and categorical features:
        - Numerical (`writing_score`, `reading_score`):
            - `SimpleImputer(strategy='median')`
            - `StandardScaler()`
        - Categorical (`gender`, `race_ethnicity`, `parental_level_of_education`, `lunch`, `test_preparation_course`):
            - `SimpleImputer(strategy='most_frequent')`
            - `OneHotEncoder()`
            - `StandardScaler(with_mean=False)`
    - Combine them using `ColumnTransformer`.
    - Fit/transform train data and transform test data.
    - Save the preprocessor as `artifacts/preprocessor.pkl`.


### 3. Model Training

- File: `model_trainer.py`[^2]
- Responsibilities:
    - Train and evaluate multiple regression models:
        - RandomForestRegressor
        - DecisionTreeRegressor
        - GradientBoostingRegressor
        - LinearRegression
        - XGBRegressor
        - AdaBoostRegressor
    - Perform hyperparameter tuning using a parameter grid per model via a helper `evaluate_models` function from `utils.py`.[^8][^2]
    - Select the best model based on R² score and ensure it meets a minimum threshold of 0.6.[^2]
    - Save the best model to `artifacts/model.pkl`.[^2]


### 4. Prediction Pipeline

- File: `predict_pipeline.py`[^9]
- Responsibilities:
    - `CustomData` class:
        - Accepts user inputs (gender, race/ethnicity, parental education, lunch, test prep, reading_score, writing_score).
        - Converts them into a Pandas DataFrame suitable for the model.
    - `PredictPipeline` class:
        - Loads `preprocessor.pkl` and `model.pkl` from `artifacts`.
        - Applies preprocessing and returns model predictions.


### 5. Logging and Exceptions

- `logger.py`: Centralized logging configuration for the pipeline.[^10]
- `exception.py`: Custom exception class wrapping errors with system info.[^11]
- `utils.py`: Helper functions such as saving objects (`save_object`) and evaluating models (`evaluate_models`).[^8]


## Web Application (Flask)

- File: `app.py`[^12][^3]
- Templates: `index.html`, `home.html`[^13][^14]

The Flask app exposes two main routes:

- `/`
    - Renders `index.html`, typically a landing or introduction page.[^3]
- `/predictdata`
    - `GET`: Renders `home.html` with the input form.[^3]
    - `POST`:
        - Reads form data: gender, ethnicity, parental education, lunch, test preparation course, reading and writing scores.
        - Wraps them into a `CustomData` object and converts to a DataFrame.[^9][^3]
        - Passes the DataFrame to `PredictPipeline` to get the predicted math score.
        - Renders `home.html` again with the prediction result displayed.[^3]

Run configuration:

- `app.run(host='0.0.0.0', debug=True)` in `app.py` to start the server.[^3]


## Notebooks

- `1.EDA_STUDENT_PERFORMANCE.ipynb`:
    - Exploratory Data Analysis on the raw dataset (distributions, correlations, feature relationships, etc.).[^15]
- `2.MODEL_TRAINING.ipynb`:
    - Model training experiments, metric comparisons, and sanity checks before moving logic into the pipeline files.[^16]

These notebooks are useful for understanding how the final pipeline design evolved.

## Installation and Usage

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/STUDENET_PERFERMANCE_ML.git
cd STUDENET_PERFERMANCE_ML
```


### 2. Create and Activate a Virtual Environment (Optional but Recommended)

```bash
python -m venv venv
source venv/bin/activate   # On Windows: venv\Scripts\activate
```


### 3. Install Requirements

```bash
pip install -r requirements.txt
```


### 4. Run the Training Pipeline (If Needed)

If you have a main script or use the modules directly, you would:

- Ingest data (produce `train.csv`, `test.csv`).[^7]
- Run data transformation and model training to generate `preprocessor.pkl` and `model.pkl` under `artifacts`.[^1][^2]

(You can orchestrate this via a driver script, or directly from a notebook.)

### 5. Start the Flask App

```bash
python app.py
```

Then open the URL shown in the terminal (usually `http://127.0.0.1:5000/`) in your browser.

### 6. Use the Web UI

- Go to the `/predictdata` page via the UI.[^3]
- Enter:
    - Gender
    - Race/ethnicity
    - Parental level of education
    - Lunch type
    - Test preparation course
    - Reading score
    - Writing score
- Submit the form to see the predicted math score rendered on `home.html`.[^14][^3]


## Project Structure

```text
.
├── app.py
├── setup.py
├── requirements.txt
├── data.csv
├── train.csv
├── test.csv
├── 1.EDA_STUDENT_PERFORMANCE.ipynb
├── 2.MODEL_TRAINING.ipynb
├── home.html
├── index.html
├── data_ingestion.py
├── data_transformation.py
├── model_trainer.py
├── predict_pipeline.py
├── logger.py
├── exception.py
├── utils.py
├── init.py
└── artifacts/
    ├── preprocessor.pkl
    └── model.pkl
```





