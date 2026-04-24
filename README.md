# MLB Pitcher Injury Risk Prediction

A data science project that predicts whether an MLB starting pitcher is at risk of an arm-related injury within the next 21 days using Statcast pitch-tracking metrics, workload features, and historical injury information.

## Project Overview

Pitcher injuries are a major challenge in Major League Baseball because they affect team performance, roster planning, and long-term player health. Prior research has often focused on season-level injury prediction, but this project builds a game-level framework that can estimate short-term injury risk after each pitcher start.

The final model predicts whether a pitcher will experience an arm-related injury within the next 21 days.

## Research Goal

The goal of this project is to answer:

> Can recent workload, velocity trends, pitch metrics, and prior injury history be used to predict short-term arm injury risk for MLB starting pitchers?

## Dataset

The dataset combines two main sources:

1. **Statcast data using `pybaseball`**  
   Used for pitch-level and game-level metrics such as velocity, spin rate, pitch movement, extension, and pitch count.

2. **MLB Stats API injury records**  
   Used to identify official injured list transactions and arm-related injuries.

The raw dataset contained about **54,000 pitcher-game observations** from **2015 to 2024**. After cleaning and preprocessing, the final dataset contained **49,792 pitcher-game rows** across **256 pitchers**.

## Target Variable

The prediction target is:

```text
injured_next_21d
```

This is a binary variable:

- `1` = pitcher suffered an arm-related injury within the next 21 days
- `0` = no injury within the next 21 days

The dataset is highly imbalanced, with only about **3.2%** of starts resulting in an injury.

## Features Used

Main feature groups included:

### Pitching Performance
- Average velocity
- Maximum velocity
- Minimum velocity
- Spin rate
- Extension
- Horizontal movement
- Vertical movement
- Number of pitch types

### Workload and Fatigue
- Pitch count
- 7-day workload
- 14-day workload
- Days of rest
- Game number in season
- Velocity drop from season average
- Rolling velocity trend
- Spin drop

### Injury Risk History
- Prior arm injury indicators
- Injury count over previous seasons
- Late-season workload indicators
- Interaction features such as workload per rest day

## Data Cleaning and Preprocessing

The following preprocessing steps were applied:

- Removed games with fewer than 10 pitches
- Removed rest-day gaps greater than 30 days to avoid cross-season gaps
- Imputed missing spin rate and pitch extension values using pitcher-season medians
- Filtered injury records to focus only on arm-related injuries such as shoulder, elbow, and forearm injuries
- Created rolling workload and velocity trend features
- Used a temporal train-test split to prevent future data leakage

## Exploratory Data Analysis

The EDA focused on:

- Injury vs non-injury class imbalance
- Injury trends across seasons
- Workload differences between injured and non-injured pitchers
- Velocity drop patterns before injury
- Distribution of days of rest and pitch count
- Relationship between prior injuries and future injury risk

Key finding: injury events are rare, so accuracy is not a reliable evaluation metric.

## Modeling Approach

A temporal split was used:

- **Training:** 2015–2021 seasons
- **Testing:** 2022–2024 seasons

This split prevents leakage from future seasons into the training process.

Models trained:

1. **Logistic Regression**  
   Used as an interpretable baseline model.

2. **Random Forest**  
   Used to capture non-linear relationships between workload, pitch metrics, and injury risk.

3. **XGBoost**  
   Used as the final recommended model because it handled class imbalance and non-linear patterns best.

Class imbalance was handled using class weighting and `scale_pos_weight`. The decision threshold was lowered from `0.5` to `0.3` to improve recall for injury cases.

## Evaluation Metrics

Because the positive injury class is rare, the project prioritized:

- Precision
- Recall
- F1-score
- PR-AUC
- MCC
- Brier Score

ROC-AUC was reported, but it was not treated as the main metric because it can look overly optimistic in highly imbalanced datasets.

## Results

XGBoost was the strongest model overall.

Key results:

- XGBoost achieved the strongest F1-score, MCC, and Brier Score
- XGBoost caught about **72% of real injuries**
- Compared with Random Forest, XGBoost generated about **41% fewer false alarms**
- Prior injury history was the strongest predictor of future arm injury
- Pitch-level metrics such as velocity drop and workload helped, but they were less powerful when used alone

## Main Conclusion

The most important finding is that pitcher injury risk appears to be cumulative. Prior injury history and long-term workload patterns were stronger predictors than single-game pitch mechanics.

This suggests that injury risk models should not rely only on velocity, pitch count, or Statcast biomechanics. They should combine performance data with historical health and workload context.

## Repository Structure

```text
MLB-Pitcher-Injury-Risk-Prediction/
│
├── README.md
├── final_dataset.csv
├── Project.ipynb
├── Project-cl.ipynb
├── Project_moreEDA.ipynb
├── DSREPORT.pdf
├── DSREPORT.docx
└── MLB_Pitcher_Injury_Prediction_Final.pptx
```

## Files in This Project

- `final_dataset.csv` — cleaned final dataset used for modeling
- `Project.ipynb` — main analysis and modeling notebook
- `Project-cl.ipynb` — data cleaning and preprocessing notebook
- `Project_moreEDA.ipynb` — additional exploratory data analysis notebook
- `DSREPORT.pdf` — final project report
- `DSREPORT.docx` — editable version of the report
- `MLB_Pitcher_Injury_Prediction_Final.pptx` — final presentation slides

## Technologies Used

- Python
- pandas
- NumPy
- scikit-learn
- XGBoost
- Matplotlib
- Seaborn
- pybaseball
- MLB Stats API
- Jupyter Notebook

## What Was Not Used

This project did not use:

- Deep learning models
- Wearable sensor data
- Medical imaging data
- Minor league or amateur pitcher data
- Real clinical injury diagnoses
- Live production deployment

The injury labels are based on MLB injured list transaction records, so some injuries may be missing or indirectly recorded.

## How to Run

1. Clone the repository:

```bash
git clone https://github.com/YOUR-USERNAME/MLB-Pitcher-Injury-Risk-Prediction.git
cd MLB-Pitcher-Injury-Risk-Prediction
```

2. Install dependencies:

```bash
pip install pandas numpy scikit-learn xgboost matplotlib seaborn pybaseball jupyter
```

3. Open the notebooks:

```bash
jupyter notebook
```

4. Run the notebooks in this suggested order:

```text
Project-cl.ipynb
Project_moreEDA.ipynb
Project.ipynb
```

## Limitations

- Injury labels come from MLB injured list records, not clinical medical records
- The model is trained only on MLB starting pitchers
- Results should not be generalized to minor league, college, or amateur pitchers
- Some injuries may be unreported or not clearly linked to arm health
- Statcast metrics alone cannot fully capture biomechanical stress

## Future Improvements

Future work could include:

- Adding wearable sensor data
- Including biomechanical elbow and shoulder stress metrics
- Testing additional time windows such as 7, 14, and 30 days
- Building a real-time dashboard for team staff
- Using survival analysis or time-to-event modeling
- Adding more detailed medical history data

## Contributors

- Syed Amaan Ahad
- Viraj Sheth
- Priyankkumar Chandrakant Patel
- Mohammad Shahid Mahmood

## References

- Kusminsky, R., et al. (2024). *Pitch-Tracking Metrics as a Predictor of Future Shoulder and Elbow Injuries in Major League Baseball Pitchers.* American Journal of Sports Medicine / PMC.
- Karnuta, J. M., et al. (2020). *Machine Learning Outperforms Regression Analysis to Predict Next-Season MLB Player Injuries.* Orthopaedic Journal of Sports Medicine.
- MLB Advanced Media. Baseball Savant Statcast Search.
- MLB Stats API injury transaction records.
