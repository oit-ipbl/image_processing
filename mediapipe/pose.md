# Tutorials for MediaPipe Pose

## Objectives
This page explains how to make a program for pose detection and get information.

## Prerequisite
You have to finish [MediaPipe (1)](mediapipe/hands.md).

## Pose landmark model
By using MediaPipe, we can obtain 3D position information of 33 landmarks as shown by the red marker in the following figure.<br>
<image src="../image/pose_tracking_full_body_landmarks.png" width="75%" height="75%"><br>

## Practice[Display all pose landmarks]
  Get information and display about pose landmarks.
  - Execute "vscode.bat" file, and open the VSCode.
  - Make a python file `mypose.py`. 
  - Type the following template. It's OK copy and paste.

### Sample code
```python
import cv2
import mediapipe as mp
mp_drawing = mp.solutions.drawing_utils
mp_pose = mp.solutions.pose

# For webcam input:
cap = cv2.VideoCapture(0)
with mp_pose.Pose(
    min_detection_confidence=0.5,
    min_tracking_confidence=0.5) as pose:
    while cap.isOpened():
        success, image = cap.read()
        if not success:
            print("Ignoring empty camera frame.")
            # If loading a video, use 'break' instead of 'continue'.
            continue

        # Flip the image horizontally for a later selfie-view display, and convert
        # the BGR image to RGB.
        image = cv2.cvtColor(cv2.flip(image, 1), cv2.COLOR_BGR2RGB)
        # To improve performance, optionally mark the image as not writeable to
        # pass by reference.
        image.flags.writeable = False
        results = pose.process(image)

        # Draw the pose annotation on the image.
        image.flags.writeable = True
        image = cv2.cvtColor(image, cv2.COLOR_RGB2BGR)
        mp_drawing.draw_landmarks(
            image, results.pose_landmarks, mp_pose.POSE_CONNECTIONS)
        cv2.imshow('MediaPipe Pose', image)
        if cv2.waitKey(5) & 0xFF == 27:
            break
cap.release()
```
  - Execute "mypose.py" by clicking the execution button.<br>
  <image src="../image/pose.png" width="30%" height="30%"><br>

### How to refer to landmark information
 - `landmark.x` and `landmark.y` are normalized to [0.0-1.0]. `landmark.z` represents  the landmark depth with the depth at the midpoint of hips being the origin, and the smaller the value the closer the landmark is to the camera.
    
## Exercise[Pose1]
 - Calculate and display the center of gravity from all the obtained landmarks.
 - Define a function that calculates and displays the center of gravity with reference to [MediaPipe (1)](mediapipe/hands.md)<br>
    <image src="../image/pose_center.png" width="30%" height="30%"><br>

### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint
It's OK, you can finish the Exercise[Pose1].
    
## Exercise[Pose2]
 - Display "right" when you raise your right hand and "left" when you raise your left hand. In addition, display "both" when you raise your both hands.
    - Use relative positions in 3D coordinates.<br>
    <image src="../image/pose_q2-1.png" width="30%" height="30%">
      <image src="../image/pose_q2-2.png" width="30%" height="30%">
        <image src="../image/pose_q2-3.png" width="30%" height="30%"><br>
### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint
It's OK, you can finish the Exercise[Pose2].
      
途中

