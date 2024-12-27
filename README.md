
# Blood Pressure Estimation using Pulse Arrival Time (PAT)

## Overview
This project aims to estimate blood pressure (BP) using Pulse Arrival Time (PAT) measurements [Finnegan et al., 2021]. The system utilizes PPG (Photoplethysmography) and ECG (Electrocardiogram) signals to calculate PAT and estimate blood pressure values through machine learning models [Chan et al., 2019].  The project follows a structured data analysis process that includes data selection, cleaning, transformation, analysis, visualization, and model building.

This project follows the data analysis process outlined in Figure 1:

![data-science](https://github.com/user-attachments/assets/14eda48d-c648-4bb1-875e-4d5bc279d540)



## Dataset Selection

Choosing a suitable dataset is crucial for building an accurate model. The ideal dataset should include simultaneous measurements of BP, PPG, and ECG, along with patient data like gender and height.

I evaluated several datasets:
1. **MIMIC-III Waveform Database** (PhysioNet) [Moody et al., 2017]
   - Largest dataset available
   - Beneficial for future project scaling
   - Challenge: Patient data not easily accessible
   - Time-intensive cleaning requirements

2. **BloodPressureDataset** (Kaggle) [Kachuee et al., 2015]
   - Well-cleaned ECG and PPG measurements
   - Includes invasive blood pressure measurements
   - Limitation: No patient demographic data

3. **Selected: Pulse Transit Time PPG Dataset** (PhysioNet) [Mehrgardt et al., 2023]
   - Manageable size
   - Consistent sensor technology
   - Complete physiological parameters (PPG, ECG, BP)
   - Includes patient demographic data

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

The first step after dataset selection is data cleaning (Figure 1). This involves assessing the quality of measurements and removing those with low signal-to-noise ratios. Smoothing filters are applied to the ECG and PPG curves to reduce noise.




### Feature Extraction

The extracted features represent characteristic traits of the PPG and ECG signals: maximum, minimum, and steepest slope (first derivative) of the PPG signal, and the R-peak of the ECG signal (Figure 2).[Kortekaas et al., 2012]

PAT is the time it takes for the pulse wave to travel from the heart (ECG R-peak) to the fingertip (PPG sensor). As PAT and heart rate correlate with BP, the following intervals are calculated [Finnegan et al., 2021]:

![Graphical-representation-of-the-calculation-of-the-pulse-arrival-time-PAT-Solid-line_W640](https://github.com/user-attachments/assets/c8e18fc7-a2fb-4bcc-ab21-9f09253dcb44)


**PPG Signal Features:**
- Maximum peak
- Minimum valley
- Steepest slope (1st derivative)

**ECG Signal Features:**
- R-peak detection
  

![output (1)](https://github.com/user-attachments/assets/4dfd5f1f-9f01-4c14-9633-47f9927ebdab)




**Extracted Features:**

*   PTTp = ECG – PPG Peak
*   PTTs = ECG – PPG Steepest Ascent
*   PTTv = ECG – PPG Valley
*   HR (Heart Rate)

For each measurement, a 10-second sequence of PPG and ECG data was used. The above-mentioned time intervals were calculated, averaged, and stored in a feature list (Figure 3). The list contains 88 measurements with three PAT intervals, heart rate, and patient data (age, height, weight, activity).


![Datenbereinigung](https://github.com/user-attachments/assets/4ca56456-ff66-4c36-99c9-2c6540193aec)

## Model Building

The feature list is used to build the models: a Multiple Linear Regression model and a Neural Network. The independent variables are PTTp, PTTs, PTTv, HR, activity, gender, height, weight, and age. The dependent variables are SBP and DBP.

1. **Multiple Linear Regression Model**
   - Baseline model for comparison

2. **Neural Network (TensorFlow)**
   - Architecture:
     - 9 input neurons
     - 5 hidden layers (50 neurons each)
     - 1 output neuron
   - Training:
     - 1000 epochs
     - Focused on minimizing prediction error

Both models are compared using two cross-validation methods.
A neural network from the TensorFlow framework was used. It has nine input neurons, one output neuron, and five hidden layers with 50 neurons each.
The network was trained for 1000 epochs.


<img width="612" alt="Modelbildung" src="https://github.com/user-attachments/assets/9fde2f8f-f20d-489f-8b77-09a3eb0b19f0" />

## Cross-Validation

Cross-validation is used to compare different models. The dataset is split into multiple parts: one for training and the rest for testing. To mitigate bias from data partitioning, this process is repeated across multiple iterations, and the average error is calculated.

Two validation methods were implemented to ensure robust model comparison:

1. **Leave-One-Out Cross-Validation**
   - 88 iterations
   - Each measurement tested once
   - Advantage: Unbiased results
   - Limitation: Susceptible to outlier influence

2. **Monte Carlo Cross-Validation**
   - 80-20 split (training-test)
   - 10000 iterations
   - Randomized dataset partitioning
   - Provides representative model performance estimation

![cross](https://github.com/user-attachments/assets/0c17112b-a503-475d-9a25-0c2403ecfdc1)

Both methods show that the Neural Network performs better for diastolic and systolic bloodpressure estimation:




Two approaches are used to represent the model's deviation from actual BP values [Wang et al., 2021]:

1.  **Mean Absolute Error (MAE):**

    $\qquad \text{MAE} = \frac{1}{n} \sum_{i=1}^n \left| \hat{Y}_i - Y_i \right|$

    The results for the neural network are: **MAE: SBP: 6.6 ± 5.1 DBP: 5.2 ± 3.9**. The figure beneath shows the comparison with the linear model. DBP is estimated more accurately than SBP.[Kurylyak et al., 2013]

![ergeb](https://github.com/user-attachments/assets/9a51d0a5-30be-40c3-bb2a-97907b0a0f62)

3.  **Mean Error (ME):**

    $\qquad \text{ME} = \frac{1}{n} \sum_{i=1}^n  \hat{Y}_i - Y_i$

    The mean should be close to zero. The Bland-Altman Plots below show these metrics: **ME: SBP: 0.06 ± 8.1 DBP: 0.58 ± 6.5**. These figures also show that the neural network does not accurately predict very high SBP and very low DBP, due to the limited number of training samples in these ranges [Finnegan et al., 2021].

The results of this project align well with existing literature [Chan et al., 2019],[Wang et al., 2021]:
![lit](https://github.com/user-attachments/assets/f63a15d6-6255-4485-ae29-6f3b53d51fbc)



 

## References

- Chan et al. (2019). "Multi-Site Photoplethysmography Technology for Blood Pressure Assessment." *Journal of Clinical Medicine*, 8(11), 1827. [Link](https://www.mdpi.com/2077-0383/8/11/1827)
- Elgendi et al. (2019). "The use of photoplethysmography for assessing hypertension." *npj Digital Medicine*, 2(1), 60. [Link](https://www.nature.com/articles/s41746-019-0136-7)
- Finnegan et al. (2021). "Pulse arrival time as a surrogate of blood pressure." *Scientific Reports*, 11(1), 22767. [Link](https://www.nature.com/articles/s41598-021-02374-0)
- Kachuee et al. (2015). "Cuff-less high-accuracy calibration-free blood pressure estimation using pulse transit time." *IEEE International Symposium on Circuits and Systems*. [Link](http://ieeexplore.ieee.org/document/7168806/)
- Kortekaas et al. (2012). "Comparison of bilateral pulse arrival time before and after induced vasodilation by axillary block." *Physiological Measurement*, 33(12), 1993-2002. [Link](https://iopscience.iop.org/article/10.1088/0967-3334/33/12/1993)
- Kurylyak et al. (2013). "A Neural Network-based method for continuous blood pressure estimation from a PPG signal." *IEEE International Instrumentation and Measurement Technology Conference*. [Link](https://ieeexplore.ieee.org/document/6555485)
- Mehrgardt et al. (2023). "Pulse Transit Time PPG Dataset." *PhysioNet*. [Link](https://physionet.org/)
- Moody et al. (2017). "MIMIC-III Waveform Database." *PhysioNet*. [Link](https://physionet.org/content/mimic-waveform-database/1.0/)
- Wang et al. (2021). "Single-Channel Bioimpedance Measurement for Wearable Continuous Blood Pressure Monitoring." *IEEE Transactions on Instrumentation and Measurement*, 70, 1-9. [Link](https://ieeexplore.ieee.org/document/9474093)

