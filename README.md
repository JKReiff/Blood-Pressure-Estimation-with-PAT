
# Blood Pressure Estimation using Pulse Arrival Time (PAT)

This project aims to estimate blood pressure (BP) using pulse arrival time (PAT). The process involves selecting a dataset, cleaning and transforming it, performing analysis and visualization, extracting key features, and finally, building a model (both Multiple Linear Regression and a Neural Network).

This project follows the data analysis process outlined in Figure [Image of Data Analysis Process - Place Holder].

## Dataset Selection

Choosing a suitable dataset is crucial for building an accurate model. The ideal dataset should include simultaneous measurements of BP, PPG, and ECG, along with patient data like gender and height.

Several datasets were considered:

*   **MIMIC-III Waveform Database (PhysioNet):** While being the largest dataset, its complexity in retrieving patient data and cleaning measurements made it unsuitable for the project's scope.
*   **BloodPressureDataset (Kaggle):** This dataset offers well-cleaned ECG and PPG measurements and includes invasive BP measurements, but lacks patient data.
*   **Pulse Transit Time PPG Dataset (PhysioNet):** This dataset was selected due to its manageable size, the sensor technology used, and the availability of patient data alongside PPG, ECG, and BP measurements.

**Dataset Composition:**

*   22 test subjects (6 female)
*   3 activities (sitting, walking, running)
*   Consistent Arduino-based sensor setup
*   BP measurements at the beginning and end of PPG/ECG recordings

**Patient Data:**

*   Non-invasive BP (cuff)
*   Weight
*   Height
*   Gender
*   Activity

## Data Cleaning and Transformation

### Data Cleaning

The first step after dataset selection is data cleaning (Figure [Image of Data Cleaning Process - Place Holder]). This involves assessing the quality of measurements and removing those with low signal-to-noise ratios. Smoothing filters are applied to the ECG and PPG curves to reduce noise (see appendix for code).

### Feature Extraction

The extracted features represent characteristic traits of the PPG and ECG signals: maximum, minimum, and steepest slope (first derivative) of the PPG signal, and the R-peak of the ECG signal (Figure [Image of PAT Calculation - Place Holder]). PAT is the time it takes for the pulse wave to travel from the heart (ECG R-peak) to the fingertip (PPG sensor). As PAT and heart rate correlate with BP, the following intervals are calculated:

**Extracted Features:**

*   PTTp = ECG – PPG Peak
*   PTTs = ECG – PPG Steepest Ascent
*   PTTv = ECG – PPG Valley
*   HR (Heart Rate)

For each measurement, a 10-second sequence of PPG and ECG data was used. The above-mentioned time intervals were calculated, averaged, and stored in a feature list (Figure [Image of Data Cleaning Process - Place Holder], see appendix for the complete list). The list contains 88 measurements with three PAT intervals, heart rate, and patient data (age, height, weight, activity).

## Model Building

The feature list is used to build the models: a Multiple Linear Regression model and a Neural Network. The independent variables are PTTp, PTTs, PTTv, HR, activity, gender, height, weight, and age. The dependent variables are SBP and DBP.

Both models are compared using two cross-validation methods.

A neural network from the TensorFlow framework was used. It has nine input neurons, one output neuron, and five hidden layers with 50 neurons each.

The network was trained for 1000 epochs.

## Cross-Validation

Cross-validation is used to compare different models. The dataset is split into multiple parts: one for training and the rest for testing. To mitigate bias from data partitioning, this process is repeated across multiple iterations, and the average error is calculated.

Leave-One-Out and Monte Carlo methods were used (Figure [Image of Cross-Validation Results - Place Holder]). The boxplots show the deviations of estimated BP values from actual BP values for both models. The Leave-One-Out method performed 88 iterations, leaving out one measurement for testing each time. The Monte Carlo method randomized the dataset into 80% training and 20% testing sets, performing 10,000 iterations.

Both methods show that the Neural Network performs better.

Two approaches are used to represent the model's deviation from actual BP values:

1.  **Mean Absolute Error (MAE):**

    $\qquad \text{MAE} = \frac{1}{n} \sum_{i=1}^n \left| \hat{Y}_i - Y_i \right|$

    The results for the neural network are: **MAE: SBP: 6.6 ± 5.1 DBP: 5.2 ± 3.9**. Figure [Image of SBP and DBP Results - Place Holder] shows the comparison with the linear model. DBP is estimated more accurately than SBP.

2.  **Mean Error (ME):**

    $\qquad \text{ME} = \frac{1}{n} \sum_{i=1}^n  \hat{Y}_i - Y_i$

    The mean should be close to zero. Figure [Image of Bland-Altman Plot SBP - Place Holder] and Figure [Image of Bland-Altman Plot DBP - Place Holder] show these metrics: **ME: SBP: 0.06 ± 8.1 DBP: 0.58 ± 6.5**. These figures also show that the neural network does not accurately predict very high SBP and very low DBP, due to the limited number of training samples in these ranges.

The results of this project align well with existing literature (Figure [Image of Literature Values - Place Holder]).

## Model Validation with Own Measurements

Own measurements from seven subjects were used to test the models. The features were extracted as shown in Figure [Image of Model Test Process - Place Holder].

Figure [Image of Model Test Results - Place Holder] shows the results for both models. The models estimate SBP better, but both BP estimates exhibit larger average deviations than in the previous chapter.

**MAE: SBP: 8.2 ± 8.6 DBP: 9.2 ± 6.8**
