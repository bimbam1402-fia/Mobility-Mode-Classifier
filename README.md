# Mobility-Mode-Classifier

## Dataset: GeoLife GPS Trajectories

This project uses the **GeoLife GPS Trajectories dataset**, collected by Microsoft Research Asia as part of the GeoLife project. The dataset contains GPS trajectories recorded by 182 users between April 2007 and August 2012. The full dataset includes 17,621 trajectories, covering about 1.2 million kilometres and more than 48,000 hours of movement data.

Each trajectory consists of time-stamped GPS points with latitude, longitude and altitude information. The data was collected using different GPS loggers and GPS-enabled phones, so the sampling rate varies between users and trajectories. Most trajectories are recorded at a relatively high density, for example every few seconds or every few metres.

For transport mode classification, it is important to note that not all trajectories contain transportation mode labels. A subset of the dataset includes labels such as walking, biking, bus, car/taxi, subway and train. In this project, these labelled parts are used to train and evaluate the classification model, while unlabelled segments can later be classified using the trained model.

The raw GPS points are transformed into movement segments, and features such as speed, acceleration, distance, duration and movement variability are calculated for each segment. These segment-level features are then used for transport mode prediction.

Dataset source: Microsoft Research GeoLife GPS Trajectories.

## Methodology

### Data Preparation
The GPS data was loaded, cleaned, and prepared for analysis. Time stamps were converted to datetime format, and movement features such as time difference, distance between points, speed, and acceleration were calculated.

### Handling Existing Labels
Where user-provided transportation labels were available, these were preserved as ground truth. The model was only used to classify segments without existing labels.

### Trajectory Segmentation
Instead of relying only on time-based trajectories, the project used speed-based segmentation. New segments were created when movement behaviour changed substantially, especially when changes in speed suggested a possible change of transport mode.

### Feature Engineering
For each segment, summary features were calculated, including mean speed, maximum speed, speed variation, acceleration, distance, duration, and number of GPS points.

### Model Training
Several classification models were tested, and Random Forest was selected as the final model because it achieved the best performance and handled the movement features well.

### Prediction of Missing Labels
The trained model was applied only to unlabelled segments. This produced predicted transport mode labels while keeping original user-provided labels unchanged.

## Results Overview

The final workflow used a speed-aware segmentation approach combined with a Random Forest classifier to predict transportation modes from GPS trajectories. Instead of classifying whole trajectory files at once, the data was first divided into shorter movement segments based on time gaps, label changes, and significant changes in speed. This allowed the model to detect more detailed travel phases, such as walking to public transport, riding a vehicle, and walking again.

The Random Forest model was trained on labelled GeoLife trajectory segments using engineered movement features such as speed, acceleration, distance, duration, stop rate, straightness ratio, and altitude-related variables. The model was evaluated using a user-based train-test split, so that the same user did not appear in both training and test data.

### Model Performance

The detailed Random Forest model performed best overall among the tested models.

| Model | Accuracy | Balanced Accuracy | Macro F1 | Weighted F1 |
|---|---:|---:|---:|---:|
| Baseline | 0.423 | 0.167 | 0.099 | 0.252 |
| Logistic Regression | 0.795 | 0.691 | 0.658 | 0.794 |
| Random Forest | 0.861 | 0.760 | 0.764 | 0.858 |
| Gradient Boosting | 0.854 | 0.749 | 0.755 | 0.852 |

Random Forest achieved the highest overall performance, with the best accuracy, macro F1-score, and weighted F1-score. Gradient Boosting performed very similarly, while Logistic Regression provided a simpler but less accurate baseline comparison.

The best-performing classes were walking and biking, which had the clearest movement patterns. Road-based and rail-based modes were harder to classify, especially bus, car/taxi, subway, and train, because these modes can have overlapping speed and acceleration patterns.

### Final Prediction Output

After evaluation, the Random Forest model was applied to the full GeoLife dataset. Original user-given labels were preserved, and the model was only used for unlabelled GPS segments.

| Category | Number of GPS points |
|---|---:|
| Total GPS points | 24,876,978 |
| Original user-labelled points | 5,425,541 |
| Random Forest predicted points | 19,372,673 |
| Still unlabelled / invalid points | 63,689 |
| Excluded rare-mode points | 15,075 |

This means that the final workflow successfully assigned predicted transportation modes to most previously unlabelled GPS points while leaving very short or unreliable segments unclassified.


### Random Forest Prediction Confidence by Mode

| Mode | Number of segments | Mean confidence | Median confidence | Min confidence | Max confidence |
|---|---:|---:|---:|---:|---:|
| Bike | 237 | 0.808 | 0.852 | 0.319 | 0.995 |
| Walk | 219 | 0.769 | 0.820 | 0.294 | 0.998 |
| Car or taxi | 76 | 0.607 | 0.570 | 0.268 | 0.983 |
| Bus | 56 | 0.545 | 0.488 | 0.265 | 0.964 |
| Subway | 14 | 0.449 | 0.442 | 0.340 | 0.606 |

The Random Forest predictions were most confident for biking and walking segments, which also had the clearest movement patterns in the model. Confidence was lower for road-based and rail-based transport modes such as car/taxi, bus, and subway, suggesting that these modes are harder to distinguish from GPS-derived movement features alone. This is expected because they can share similar speed and acceleration patterns, especially in urban traffic or stop-and-go movement.

### Findings

- Speed-aware segmentation produced more realistic movement phases than simple time-based trajectory splitting.
- Random Forest was the best practical model for the detailed classification task.
- Speed-related features were the most important predictors, but the model also used acceleration, distance, duration, stop rate, straightness, and altitude features.
- Walking and biking were classified most confidently.
- Bus, car/taxi, subway, and train were more difficult due to overlapping movement behaviour and GPS noise.
- The final per-user parquet output is suitable for further analysis and map-based trajectory visualization.
