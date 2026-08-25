# GDG Event Attendance Prediction

A machine learning project that predicts whether a registered student is likely to attend a GDG event. The project analyzes registration and student-related information to estimate **attendance probability**, classify **attendance risk**, and provide useful insights for event organizers.

---

## 📌 Problem Statement

Event registration does not always guarantee attendance. No-shows can make event planning difficult because organizers may have limited visibility into how many registered participants are actually likely to attend.

This project uses historical event registration data to build a machine learning classification model that predicts whether a registered student will:

* **Attend**
* **Not attend (No-show)**

In addition to the prediction, the system provides an estimated **attendance probability** and a **risk category**.

---

## 🎯 Objective

The main objectives of this project are to:

* Analyze historical event registration and attendance data.
* Clean and preprocess inconsistent or missing data.
* Train and compare multiple machine learning classification models.
* Select a suitable model based on evaluation metrics.
* Predict attendance for unseen test registrations.
* Generate attendance probabilities.
* Classify registrations into different risk levels.
* Identify important features influencing the model's predictions.
* Provide useful analytics for event organizers.

---

## 📊 Dataset

The project uses two datasets provided for the GDG task:

* **Training Dataset:** 508 rows × 10 columns
* **Test Dataset:** 100 rows × 9 columns

The training dataset contains the target variable `attended`, while the test dataset is used to generate final predictions.

### Features

| Feature                      | Description                                                |
| ---------------------------- | ---------------------------------------------------------- |
| `student_id`                 | Unique identifier of the student                           |
| `event_type`                 | Type of event                                              |
| `registration_days_before`   | Number of days before the event when registration occurred |
| `previous_events_registered` | Number of previous events registered for                   |
| `previous_events_attended`   | Number of previous events attended                         |
| `club_member`                | Whether the student is a club member                       |
| `event_day`                  | Day on which the event takes place                         |
| `event_time`                 | Event starting time                                        |
| `travel_distance_km`         | Approximate distance travelled to attend the event         |
| `attended`                   | Target variable: 1 for attended and 0 for no-show          |

`student_id` is used only for identifying predictions and is not used as a model feature.

> **Note:** The original GDG datasets are not included in this public repository.

---

## 🔎 Data Exploration

The dataset was inspected to understand:

* Dataset dimensions
* Column types
* Missing values
* Unique and repeated values
* Numerical statistics
* Categorical distributions
* Potential outliers
* Inconsistent values

Several data-quality issues were identified, including inconsistent capitalization in categorical values, missing values, invalid registration-day values, and inconsistent previous attendance records.

---

## 🧹 Data Preprocessing

The following preprocessing steps were performed:

### 1. Categorical Data Cleaning

Inconsistent categorical values such as:

```text
Workshop
workshop
HACKATHON
Hackathon
```

and:

```text
Yes
yes
YES
```

were standardized.

### 2. Invalid Registration Values

`registration_days_before` values outside the expected range of **0–14 days** were treated as missing values.

### 3. Previous Attendance Validation

Records where:

```text
previous_events_attended >
previous_events_registered
```

were treated as invalid and the attendance value was replaced with a missing value.

### 4. Missing Values

Numerical features were handled using **median imputation**.

Categorical features were handled using **most-frequent-value imputation**.

### 5. Categorical Encoding

Categorical features were converted into numerical representations using **One-Hot Encoding**.

### 6. Target Cleaning

Rows with missing values in the target variable `attended` were removed before model training.

---

## 🤖 Machine Learning Models

Four classification models were trained and evaluated:

1. Logistic Regression
2. Decision Tree
3. Random Forest
4. Gradient Boosting

The models were evaluated using a stratified train-validation split.

---

## 📈 Model Evaluation

The following metrics were used:

* **Accuracy** — Overall proportion of correct predictions.
* **Precision** — Proportion of predicted attendees who actually attended.
* **Recall** — Proportion of actual attendees correctly identified.
* **F1-score** — Balance between precision and recall.

### Results

| Model               |   Accuracy |  Precision |     Recall |   F1-score |
| ------------------- | ---------: | ---------: | ---------: | ---------: |
| **Random Forest**   | **68.32%** | **71.62%** | **82.81%** | **76.81%** |
| Gradient Boosting   |     67.33% |     71.23% |     81.25% |     75.91% |
| Decision Tree       |     65.35% |     71.01% |     76.56% |     73.68% |
| Logistic Regression |     63.37% |     68.49% |     78.13% |     72.99% |

### 🏆 Final Model

**Random Forest** was selected as the final model because it achieved the highest F1-score among the four evaluated models while also providing strong recall.

The final Random Forest model was then retrained using the available cleaned training data before generating predictions for the test dataset.

---

## 🎯 Attendance Prediction

For every test registration, the final model generates:

* Attendance prediction
* Attendance probability
* Risk level
* Human-readable prediction label

Example:

| Student ID | Probability | Prediction | Risk Level        |
| ---------- | ----------: | ---------- | ----------------- |
| S1379      |         96% | Attend     | High likelihood   |
| S1425      |         92% | Attend     | High likelihood   |
| S1273      |          8% | No-show    | High no-show risk |
| S1028      |         19% | No-show    | High no-show risk |
| S1204      |         57% | Attend     | Medium likelihood |

---

## ⚠️ Risk Classification

Attendance probability is converted into three categories:

| Probability | Risk Level        |
| ----------: | ----------------- |
|       ≥ 70% | High likelihood   |
|   40% – 69% | Medium likelihood |
|       < 40% | High no-show risk |

This provides organizers with an easier way to interpret the model's probability predictions.

---

## 📊 Test Dataset Results

The final model generated predictions for **100 registrations**.

### Summary

* **Total registrations:** 100
* **Predicted attendees:** 62
* **Predicted no-shows:** 38
* **Average attendance probability:** 62.98%

### Risk Distribution

| Risk Level        | Count |
| ----------------- | ----: |
| High likelihood   |    61 |
| Medium likelihood |     1 |
| High no-show risk |    38 |

The complete predictions are saved in:

```text
outputs/attendance_predictions.csv
```

---

## 🔍 Feature Importance

Random Forest feature importance was used to understand which features contributed most to the model's predictions.

### Top Features

| Feature                        | Importance |
| ------------------------------ | ---------: |
| Travel distance                |     0.1801 |
| Registration days before event |     0.1649 |
| Previous events registered     |     0.1040 |
| Previous events attended       |     0.0921 |
| Event type – Workshop          |     0.0345 |

Other features such as event time, event day, club membership, and event type also contributed to the model.

> **Important:** Feature importance indicates how useful a feature was to the model's decision-making process. It does not establish a causal relationship between the feature and attendance.

---

## 📊 Analytics and Visualizations

The project includes visualizations for:

### Predicted Attendance

Shows the number of registrations predicted to attend and not attend.

### Attendance Risk Distribution

Shows the distribution of registrations across the three risk categories.

### Feature Importance

Shows the top features contributing to the Random Forest model.

### Model Performance Comparison

Compares Accuracy, Precision, Recall, and F1-score across the four trained models.

---

## 💡 Key Insights

The analysis and model indicate that several factors are particularly useful for predicting attendance, including:

* Travel distance
* How early the student registered
* Previous registration history
* Previous attendance history
* Event type

These insights can help organizers better understand attendance patterns and plan events more effectively.

---

## 🛠️ Technologies Used

* **Python**
* **Pandas**
* **NumPy**
* **Scikit-learn**
* **Matplotlib**
* **Seaborn**
* **Joblib**
* **Google Colab**
* **Jupyter Notebook**
* **GitHub**

---

## 📚 Key Learnings

Through this project, I gained practical experience in:

* Understanding and cleaning real-world datasets
* Handling missing and inconsistent data
* Exploratory data analysis
* Feature selection
* Numerical and categorical preprocessing
* One-Hot Encoding
* Building machine learning pipelines
* Training classification models
* Comparing multiple ML algorithms
* Evaluating models using Accuracy, Precision, Recall, and F1-score
* Generating probability-based predictions
* Feature importance analysis
* Creating data visualizations
* Saving trained models using Joblib
* Structuring and documenting an ML project for GitHub

---

## ⚠️ Limitations

* The dataset is relatively small.
* Model performance depends on the quality and representativeness of the available training data.
* Predictions may not generalize perfectly to different events or student populations.
* Feature importance should not be interpreted as causation.
* The current implementation is an offline prediction system and does not include real-time event-management or reminder APIs.

---

## 🚀 Future Improvements

Possible future improvements include:

* Developing an interactive Streamlit dashboard.
* Integrating the model with an event registration system.
* Adding automated model retraining as new attendance data becomes available.
* Improving probability calibration.
* Adding more advanced model explainability.
* Integrating automated attendance reminders.
* Deploying the prediction system as a web application.

---

## 📓 Notebook

The complete implementation, including data exploration, preprocessing, model training, evaluation, prediction, feature importance, and visualizations, is available in:

```text
notebooks/GDG_Event_Attendance_Prediction.ipynb
```

---


## 👤 Author

**Khanjan Kasindariya**

Computer Engineering Student
Dharamsinh Desai University (DDU)

---

## 📄 License

This project is created for educational and hackathon/task purposes.

The dataset used for this project was provided as part of the GDG task and is not redistributed in this repository.
