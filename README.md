# Project 2: Sensor Fusion and Object Detection

- Write a short recap of the four tracking steps and what you implemented there (EKF, track management, data association, camera-lidar sensor fusion). Which results did you achieve? Which part of the project was most difficult for you to complete, and why?
- Do you see any benefits in camera-lidar fusion compared to lidar-only tracking (in theory and in your concrete results)?
- Which challenges will a sensor fusion system face in real-life scenarios? Did you see any of these challenges in the project?
- Can you think of ways to improve your tracking results in the future?

## Step 1: Extended Kalman Filter `filter.py` 

In this step, a standard extended kalman filter is implemented using the measurements from a 3D lidar detection. This filter uses a kinematic model to predict the detected objects. The state prediction and update is implemented with covariance matrices and time step (.1s) as provided in `misc/params.py`.
Shown below is a visualization of EKF on one the Waymo Datasets. 

![Camera Track : Step 1](results/end_module/Step1_CameraTrack.png)
*(Left) Camera output with bounding box predicted by EKF. (Right) Tracking in BEV* 

![RMSE : Step 1](results/end_module/Step1_RMSE.png)
*RMSE plot for prediction error with respect to ground truth*

## Step 2: Track management `track_management.py` 

In this step, the track management algorithm is implemented. Each unassigned measurement is initilized as track. If that measurement continues to match with prediction, the tracking score is increases until 1. Once the score reaches a minimum threshold, the track confirms the location of object and if the score drops below another threshold, it gets deleted, otherwise it is kept as tentative. 

![RMSE : Step 2](results/end_module/Step2_RMSE.png)
*RMSE plot for prediction error with respect to ground truth*

Clearly there is a large RMSE, describing a poor performance. This can be because the object is moving relatively fast through the frames and the Prediction is not able to track it properly.

Personally, I found this part a little bit difficult to implement. This section was written in a more intuitive than theoretic. I had to look the system as state machine and had to deal with thresholds. This difficulty is may be because I take interest in control systems and it generally involves dynamic systems with continous domain. That is why I found particularly EKF more easy to comprehend than this.

## Step 3: Track association `association.py`

In this step, the algorithm makes sure that with each new timestep, different tracks are associated with their respective ground truth. This step only uses the lidar measurements.

![RMSE : Step 3](results/end_module/Step3_trackingRMSE_.png)
*The tracking shows a ghost output *

Below Shows the Video output after step 3

https://user-images.githubusercontent.com/16174913/222326607-f3c9a398-f009-4023-83ac-53efe13da70f.mp4

## Step 4: Camera+Lidar Fusion `measurements.py`

Finally in this step, the nonlinear measurement model for camera is implemented.  

![RMSE : Step 4](results/end_module/Step4_RMSE.png)
*RMSE plot for prediction error with respect to ground truth*

The Video after final step can be observed as below

https://user-images.githubusercontent.com/16174913/222013937-a4127b27-73b4-4d0d-b7bd-a4fbe98e62d8.mp4

## Lidar Only vs Camera+Lidar
In theory for kalman filter, the prediction step increases the state covariance whereas the update step decreases it. And more the number of sensors implies more the update steps as compared to prediction, and hence always gives better the estimate. Intuitively, more measurement brings more information about the system.
Comparing the outputs from Step 3 and Step 4 show that adding the Camera model does help an improvement in tracking performance for few tracks (distant car at track 11). This can be observed through their respective RMSE plots as well.

## Challenges in real life-scenerio
- Here only straight motion of the objects was observed. Vehicles can move on ramps, can stop, do a lane change etc.   
- The sensor noise of measurements can vary depending upon the environment. For example, a camera measurments might be be noise during nights or Lidar sensor might detect false positives in occulded road. The training dataset here contained a simple scenerio.
- Any fault on the sensors, can cause the system to loose tracking, like mud on camera or lidar failure. 
- For other objects like, Pedestrian, cyclists etc, the motion prediction model might not be suffice and can be improved upon.    

## Improvements
- For this particular project, use of other CNN models for object detection can be tried out.
- The parameters can be better tuned to improve the RMSE

