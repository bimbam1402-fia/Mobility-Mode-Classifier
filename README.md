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

### Visualization
The resulting labels can be used for map-based visualization, where different transport modes are shown in different colors along the trajectory and potentially animated over time.
