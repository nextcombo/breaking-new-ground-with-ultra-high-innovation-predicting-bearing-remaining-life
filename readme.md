# Breaking new ground with ultra-high innovation! Predicting bearing remaining life based on TVFEMD-parallel GRU-Informer + SHAP analysis, exceedingly effective!

Note: This project uses the Python language!
1. Research background: This project proposes a novel method for predicting the Replacement Time (RUL) of bearings using a dual-branch hybrid model of TVFEMD denoising + GRU-Informer. The comparison with six excellent models from recent years has been made, and the code for these models is already written. These six models include:
   - gru_informer
   - informer
   - gru
   - conv_lstm
   - transformer
   - patch_tst
   - mamba
2. Overall framework
3. Data set usage: The IEEE PHM 2012 Bearing Full Lifespan Degradation dataset is used.
4. Working condition condition 1: Seven bearings (Bearing1_1 ~ Bearing1_7) are tested under this condition.
5. Sampling frequency: 25.6 kHz, with each sampling lasting 0.1 seconds (2560 data points), and every 10 seconds once.
6. Each bearing's degradation process starts from healthy operation to failure, with sampling numbers ranging from over 800 to more than 2800 times.
4. Feature extraction and TVFEMD denoising
4.1 33-dimensional feature extraction: For each vibrational sample (2560 points), 33-dimensional features are extracted:
   - Time domain features (18): Mean, Variance, Kurtosis, Skewness, RMS, Peak factor, Wavelet factor, Pulse factor, Marginal factor, Shannon entropy, etc.
   - Frequency domain features (15): Hilbert envelope features, Spectral centroid, Spectral extension, Spectral entropy, Main frequency, Energy proportion of four frequency bands, etc.
Thus, each bearing obtains a degradation trend curve of 33 time-varying features.
4.2 TVFEMD denoising for degradation trend: In the original feature degradation curves, there are many fluctuations, not all changes reflect real degradation. We use TVFEMD (Time-Varying Filtered Empirical Mode Decomposition) to denoise each trend curve:
Decomposition: The degraded curve is divided into multiple IMF (Intrinsic Mode Function) components, arranged from high frequency to low frequency.
Energy proportion filtering: Calculate the energy proportion of each IMF component to the total energy of all IMFs, retaining components with energy proportion ≥ 3%.
Noise reduction reconstruction: Sums up the retained IMF components to obtain the denoised degradation trend.
Compared to the traditional IMF selection method based on correlation coefficients, energy proportion filtering has a more physical significance—high-frequency noise components naturally have lower energy and can be effectively filtered out.
Here, we demonstrate the noise reduction effect of Tvfemd on the indicators using Bearing1_1 as an example.

The remaining bearings are not shown one by one; code execution can perform feature extraction and noise reduction for all bearings.

4.3 Sliding windows divide the denoised 33-dimensional features into time series samples with window size=10 and stride=1, resulting in time-series samples like (n_windows, 10, 33). Each sample contains consecutive 10 sampling moments of 33-dimensional features, preserving the complete temporal structure.
RUL labels are normalized to [0, 1], where 1.0 represents the early stage of bearing life and 0.0 indicates the failure moment.
V. Model Architecture 5.1 GRU-Informer Dual-Branch Mixed Model The core model GRU-Informer adopts a dual-branch structure:

Core design concept:
Informer branch: Transposes the input to variable-level perspective, and models the mutual dependencies among 33 features through ProbSparse self-attention mechanism. ProbSparse attention only calculates full attention for the most active query, reducing computational complexity.
GRU branch: Bidirectional GRU directly models the degenerative trend within 10 time steps in a window.
Attention fusion: The outputs of two branches are concatenated, and through a learned softmax attention weight dynamically determines the contribution of each branch, achieving adaptive fusion.
5.2 To validate the effectiveness of the GRU-Informer, the project also implemented six contrastive models:
All models are fed with [batch, 10, 33] as input, and output [batch, 1], fair comparison under the same framework.
Sixth, the project supports flexible experiment configurations:
Custom training/test sets: freely specify which bearings to train and which to test.
Leave-one-out cross-validation: Each time leave one bearing for testing, and train the remaining 6, totaling 7 rounds of experiments.
Evaluation metrics MSE (Mean Squared Error), RMSE (Root Mean Square Error), MAE (Mean Absolute Error)
R2 (Coefficient of Determination, closer to 1 is better)
MAPE (Mean Absolute Percentage Error)
SCORE (Bearing Life Prediction Evaluation Indicator)
Seventh, using cross-validation, we alternately test and validate the robustness and generalization ability of the model. For example, if we train on bearings 1, 2, 4, 5, 6, 7, then we test on bearing 3. Continuously do this until all bearings have been tested. The prediction results for Bearing1_3-1_7 are as follows:
Based on TVFEMD-parallel GRU-Informer, the life prediction results are as follows:
Bearing1-3 prediction results:
The TVFEMD-parallel GRU-Informer model achieved a R2 fitting rate of 98% on the Bearing1_3 bearing test, with an RMSE directly reaching around 0.03. This is remarkable considering that many top-tier SCI journals achieve an RMSE of only about 0.5. This demonstrates the feasibility of the TVFEMD noise reduction approach!
Here is a result from a paper (Adaptive Res-LSTM Attention-based Remaining Useful Lifetime Prognosis of Rolling Bearings) for comparison purposes (this is just for contrast and does not mean to compare unfavorably!)
Bearing1-4 Prediction Results:

Bearing1-5 Prediction Results:

Bearing1-6 Prediction Results:

Bearing1-7 Prediction Results:
Comparative Analysis Among Multi-Model
Compared with informer, gru, conv_lstm, transformer, patch_tst, mamba, the results are as follows:
Bearing1-3 Multi-Model Prediction Comparative Results:
1. Bearing 1-4 Multi-Model Prediction Results:
   A thorough comparison experiment has been conducted, and the remaining bearings will not be displayed one by one.
Here is a summary of the average test results for all bearings 3-7:
In conclusion, you can see that this project has completed all comparative experiments! On the PHM 2012 dataset, the GRU-Informer model achieved very high prediction accuracy, outperforming pure GRU, Transformer, Informer, PatchTST, Mamba, and other comparative models.
8. SHAP Explanability Analysis
The project also integrates the SHAP (SHapley Additive exPlanations) analysis module, which allows for intuitive visualization of which features contribute most to RUL (Reliability Uptime) prediction, providing a basis for understanding model decisions.
9. Summary
This project proposes a method for bearing RUL prediction that combines signal processing with deep learning:
TVFEMD Denoising: Based on energy proportion selection, it effectively removes noise fluctuations in feature degradation curves while preserving the true degradation trend.
GRU-Informer Mixed Model: The Informer branch captures feature interdependencies, while the GRU branch captures the temporal degradation trend, adaptively integrating through attention weighting.
Complete Experimental Framework: Supports 7 types of model comparisons, leave-one-out cross-validation, custom training/test splitting, and SHAP explainability analysis.
On the PHM 2012 dataset, the GRU-Informer model achieved a very high prediction accuracy, outperforming pure GRU, Transformer, Informer, PatchTST, Mamba and other comparison models.

## Images

![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](01.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](02.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](03.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](04.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](05.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](06.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](07.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](08.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](09.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](10.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](11.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](12.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](13.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](14.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](15.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](16.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](17.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](18.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](19.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](20.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](21.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](22.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](23.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](24.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](25.png)
![High-level innovation has arrived! Predicting the remaining life of bearings based on TVFEMD-parallel GRU-Informer + SHAP analysis, with outstanding results!](26.png)

<!-- PAYMENT_BLOCK -->

## Get this dataset

- Payment link (Stripe): [Buy now](https://buy.stripe.com/3cs8yP7sY87d0vu9AB)
- Payment QR code: ![Payment QR code](pay.png)

<!-- /PAYMENT_BLOCK -->
