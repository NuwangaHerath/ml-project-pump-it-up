# ml-project-pump-it-up
Github repo link - https://github.com/NuwangaHerath/ml-project-pump-it-up

## Problem Description
The data for this competition comes from the Taarifa waterpoints dashboard, which aggregates data from the Tanzania Ministry of Water.

The task is to predict the operating condition of a waterpoint for each record in the dataset. We are provided the following set of information about the waterpoints: 


    amount_tsh - Total static head (amount water available to waterpoint)
    date_recorded - The date the row was entered
    funder - Who funded the well
    gps_height - Altitude of the well
    installer - Organization that installed the well
    longitude - GPS coordinate
    latitude - GPS coordinate
    wpt_name - Name of the waterpoint if there is one
    num_private -
    basin - Geographic water basin
    subvillage - Geographic location
    region - Geographic location
    region_code - Geographic location (coded)
    district_code - Geographic location (coded)
    lga - Geographic location
    ward - Geographic location
    population - Population around the well
    public_meeting - True/False
    recorded_by - Group entering this row of data
    scheme_management - Who operates the waterpoint
    scheme_name - Who operates the waterpoint
    permit - If the waterpoint is permitted
    construction_year - Year the waterpoint was constructed
    extraction_type - The kind of extraction the waterpoint uses
    extraction_type_group - The kind of extraction the waterpoint uses
    extraction_type_class - The kind of extraction the waterpoint uses
    management - How the waterpoint is managed
    management_group - How the waterpoint is managed
    payment - What the water costs
    payment_type - What the water costs
    water_quality - The quality of the water
    quality_group - The quality of the water
    quantity - The quantity of water
    quantity_group - The quantity of water
    source - The source of the water
    source_type - The source of the water
    source_class - The source of the water
    waterpoint_type - The kind of waterpoint
    waterpoint_type_group - The kind of waterpoint

## Approach
### Step 1 - Load the dataset
The given datasets were uploaded to google drive and then mounted the drive into Google Colab notebook. Then loaded the datasets to the notebook.

### Step 2 - Exploratory Data Analysis (EDA)
Here I manually explored the dataset get an idea about the dataset and data types of the features set. First I checked out the data types and then created two list of categorical feature columns and numerical feature columns for furthur works.

For the categorical features, I plotted count plots to get insight about the data and their distribution.

For the numerical features, I plotted distplots to get insight about the data and their distribution.

### Decisions after exploring the data manually
**High categorical cardinality**
*   ``date_recorded`` will be extract into three featutes, ``year_recorded``, ``month_recorded``, ``day_recorded``.
*   ``wpt_name`` and ``subvillage`` will be removed from input dataset as the higher categorical cardinality. Cannot expect info gain from those features.

**Duplicate features**
* ``region`` and ``region_code`` ----> ``region`` will be removed.
* ``management`` and ``scheme_management`` -------> after impute the NaN values, two features will be merged into one.
* from ``extraction_type``, ``extraction_type_group`` and ``extraction_type_class`` will select one.
* ``payment`` and ``payment_type``  ------> ``payment`` will be removed.
* from ``quantity`` and ``quantity_group`` --------> ``quantity_group`` will be removed.
* from ``source`` and ``source_type`` will select a one.
* from ``waterpoint_type`` and ``waterpoint_type_group`` ---------> ``waterpoint_type_group`` will be removed.

**Removals**
* ``num_private`` only have 0 as values. So that column should drop.

### Step 3 - Remove duplicates and unnecessary columns
Following columns were removed from the input dataset according to the decisions taken from EDA.

``[wpt_name, subvillage, region, extraction_type_group, extraction_type_class, payment, quantity_group, source_type, waterpoint_type_group, num_private, recorded_by]``

### Step 4 - Handling Outliers

I plotted the box plots for numerical features and there were some significant outlier looking abnormal distributions and found out that those features showed that distribution due to lots of ``0`` values. Then I looked for that particular columns to find out the count of the ``0`` values. Followings are the 0 counts.

* ``population`` - 21381 (Population around a pump cannot be 0 for such large number.)
* ``gps_height`` - 20438 (This cannot be happen as the gps_height of that area not nearly be 0.)
* ``longitude`` - 1812 (This cannot be happen as longitude of that are lies between 33 - 39 approx.)
* ``amount_tsh`` - 41639 (Static head of a pump cannot be 0 for such large number.)

To impute those ``0`` values, I used several combinations of grouping techniques and imputaion techniques(eg: mean,mode, median)

### Step 5 - Imputing null values

There were null values only in categorical feature columns.

* ``management`` and ``scheme management`` have similar values. So ``NaN`` values of both columns imputed with corrusponding values in the other column. If both corrusponding column values were ``NaN`` they were imputed  with other techniques.
* Mode imputation were performed for other categorical columns with null values.

### Step 6 - Feature Extraction and New Feature Creation

* ``date_recorded`` were extracted into ``year_recorded``, ``month_recorded``, ``day_recorded``.
* Introduced a new feature ``pump_age``, where ``pump_age = (year_recorded - construction_year)``.

### Step 7 - Encoding Categorical Features

Used **Target Encoding** to encode the categorical features.

### Step 8 - Scaling the Data

Used **Standard Scalar** to normalized the input dataset.

### Step 9 - Feature Importance

As I were used both XGBoost Classifier and CatBoost Classifier for training, I checked out the feature importances using XGBoost feature importance and CatBoost feature importance functionalities. And less importance columns were dropped from input dataset.

### Training and Evaluation

I used XGBoost and CatBoost as classification models and hoped to get better performanced model for final submission.
* **Hyper Parameter Tuning** were applied with **random search** for catboost classifer.
* **early stopping** was used to prevent overfiting.
* According to my setting **CatBoost Classifier** was given the better performances.

### Submissions

* Best Score   - 0.8175
* Current Rank - 1544
