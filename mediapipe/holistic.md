# Tutorials for MediaPipe Holistic

## Objectives
This page explains how to make a program for hands, pose and face detection and get information.

## Prerequisite
You have to finish [MediaPipe Face](../mediapipe/face.md).

## Face landmark model
MediaPipe Holistic utilizes the pose, face and hand landmark models,  respectively to generate a total of 543 landmarks (33 pose landmarks, 468 face landmarks, and 21 hand landmarks per hand).<br>
<image src="../image/holistic_sports_and_gestures_example.gif" width="30%" height="30%">

## Practice[Display all face landmarks]
  Get information and display about holistic landmarks.
  - Execute "vscode.bat" file, and open the VSCode.
  - Make a python file `myholistic.py`. 
  - Type the following template. It's OK copy and paste.

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


## Exercise[Face1]
 - Calculate the center of gravity of all face landmarks, and draw green circle.<br>
    <image src="../image/q1_face.png" width="30%" height="30%"><br>

### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint
It's OK, you can finish the Exercise[Face1].

## Exercise[Face2]
 - Display "left" or "right" according to the orientation of the face.<br>
    <image src="../image/q2_face.gif" width="30%" height="30%"><br>

### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint
It's OK, you can finish the Exercise[Face2].

## Challenge[Face1]
 - Display the directions randomly, and display the number of times you turned in the same direction.<br>
 <image src="../image/ques.gif" width="30%" height="30%"><br>
 - Use the following code to randomly generate an integer. In this code, `random.randint(0, 5)` returns a random integer int with `0<=n<=5`.
````python
    import random
    random.randint(0, 5)
````
 - You can randomly select and display the elements of the array with the following code.
````python
   msg_array = ("msg1", "msg2", "msg3")
   select = msg_array[random.randint(0, len(msg_array)-1)]
   print(select)
````
   
## Challenge[Face2]
 - Display "smile" when smile. 
 - The position of facial parts is the basic information for classifying facial expressions.
 - Think so that you can judge correctly even if you tilt your face.<br>
<image src="../image/smile.gif" width="30%" height="30%"><br>


---

[README](../README.md)
