# Tutorials for MediaPipe Holistic

## Objectives
This page explains how to make a program for hands, pose and face detection and get information.

## Prerequisite
You have to finish [MediaPipe Face](../mediapipe/face.md).

<!--MediaPipeのページにはLandmark modelsと書いてました-->
## Landmark models
MediaPipe Holistic utilizes the pose, face and hand landmark models,  respectively to generate a total of 543 landmarks (33 pose landmarks, 468 face landmarks, and 21 hand landmarks per hand).<br>
<image src="../image/holistic_sports_and_gestures_example.gif" width="50%" height="50%">

<!--faceではないような-->
## Practice[Display all holistic landmarks]
  Get information and display about holistic landmarks.
  - Execute "vscode.bat" file, and open the VSCode.
  - Make a python file `myholistic.py`. 
  - Type the following sample code. It's OK copy and paste.

### Sample code
```python
import cv2
import mediapipe as mp
mp_drawing = mp.solutions.drawing_utils
mp_holistic = mp.solutions.holistic

# For webcam input:
cap = cv2.VideoCapture(0)
with mp_holistic.Holistic(
    min_detection_confidence=0.5,
    min_tracking_confidence=0.5) as holistic:
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
    results = holistic.process(image)

    # Draw landmark annotation on the image.
    image.flags.writeable = True
    image = cv2.cvtColor(image, cv2.COLOR_RGB2BGR)
    mp_drawing.draw_landmarks(
        image, results.face_landmarks, mp_holistic.FACE_CONNECTIONS)
    mp_drawing.draw_landmarks(
        image, results.left_hand_landmarks, mp_holistic.HAND_CONNECTIONS)
    mp_drawing.draw_landmarks(
        image, results.right_hand_landmarks, mp_holistic.HAND_CONNECTIONS)
    mp_drawing.draw_landmarks(
        image, results.pose_landmarks, mp_holistic.POSE_CONNECTIONS)
    cv2.imshow('MediaPipe Holistic', image)
    if cv2.waitKey(5) & 0xFF == 27:
      break
cap.release()
```
  - Execute "myholistic.py" by clicking the execution button.<br>
  <image src="../image/holistic.png" width="30%" height="30%"><br>
  - If you want to stop this program, press "Esc" key while the preview window is active.

<!--ちょっと難しすぎるかもしれません．また，手，ポーズ，顔それぞれを別々に利用するだけであればHolisticは不要かもと思いました．HolisticそのものをChallengeで，余裕がある人だけやってみようみたいなので良いかもしれません．現状では恐らくほとんどの学生ができないのでは．．-->
## Exercise[Holistic1]
 - Make a simple game like [Exercise[Face3]](../mediapipe/face.md#exerciseface3) using the information from **hands**.

### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint
It's OK, you can finish the Exercise[Holistic1].

## Exercise[Holistic2]
 - Make a simple game like [Exercise[Face3]](../mediapipe/face.md#exerciseface3) using the information from **pose**.

### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint
It's OK, you can finish the Exercise[Holistic2].

## Exercise[Holistic3]
 - Make a simple game like [Exercise[Face3]](../mediapipe/face.md#exerciseface3) using the information from **face**.

### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint
It's OK, you can finish the Exercise[Holistic3].
    
---

[README](../README.md)
