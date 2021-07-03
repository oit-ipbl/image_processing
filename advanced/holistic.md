# Tutorials for MediaPipe Holistic

## Objectives
This page explains how to make a program for hands, pose and face detection and get information.

## Prerequisite
You have to finish [MediaPipe Face](../mediapipe/face.md).

## Face landmark model
MediaPipe Holistic utilizes the pose, face and hand landmark models,  respectively to generate a total of 543 landmarks (33 pose landmarks, 468 face landmarks, and 21 hand landmarks per hand).<br>
<image src="../image/holistic_sports_and_gestures_example.gif" width="50%" height="50%">

## Practice[Display all face landmarks]
  Get information and display about holistic landmarks.
  - Execute "vscode.bat" file, and open the VSCode.
  - Make a python file `myholistic.py`. 
  - Type the following sample code. It's OK copy and paste.

### Sample code
```python
import cv2
import mediapipe as mp
import time
mp_drawing = mp.solutions.drawing_utils
mp_holistic = mp.solutions.holistic

device = 0 # camera device number

def getFrameNumber(start:float, fps:int):
    now = time.perf_counter() - start
    frame_now = int(now * 1000 / fps)

    return frame_now

def main():
    # For webcam input:
    global device

    cap = cv2.VideoCapture(device)
    fps = cap.get(cv2.CAP_PROP_FPS)
    wt  = cap.get(cv2.CAP_PROP_FRAME_WIDTH)
    ht  = cap.get(cv2.CAP_PROP_FRAME_HEIGHT)

    print("Size:", ht, "x", wt, "/Fps: ", fps)

    start = time.perf_counter()
    frame_prv = -1
    with mp_holistic.Holistic(
        min_detection_confidence=0.5,
        min_tracking_confidence=0.5) as holistic:
        while cap.isOpened():
            frame_now=getFrameNumber(start, fps)
            if frame_now == frame_prv:
                continue
            frame_prv = frame_now

            ret, frame = cap.read()
            if not ret:
                print("Ignoring empty camera frame.")
                # If loading a video, use 'break' instead of 'continue'.
                continue

            # Flip the image horizontally for a later selfie-view display, and convert
            # the BGR image to RGB.
            frame = cv2.cvtColor(cv2.flip(frame, 1), cv2.COLOR_BGR2RGB)
            # To improve performance, optionally mark the image as not writeable to
            # pass by reference.
            frame.flags.writeable = False
            results = holistic.process(frame)

            # Draw landmark annotation on the image.
            frame.flags.writeable = True
            frame = cv2.cvtColor(frame, cv2.COLOR_RGB2BGR)
            mp_drawing.draw_landmarks(frame, results.face_landmarks, mp_holistic.FACE_CONNECTIONS)
            mp_drawing.draw_landmarks(frame, results.left_hand_landmarks, mp_holistic.HAND_CONNECTIONS)
            mp_drawing.draw_landmarks(frame, results.right_hand_landmarks, mp_holistic.HAND_CONNECTIONS)
            mp_drawing.draw_landmarks(frame, results.pose_landmarks, mp_holistic.POSE_CONNECTIONS)
            cv2.imshow('MediaPipe Holistic', frame)
            if cv2.waitKey(5) & 0xFF == 27:
                break
    cap.release()

if __name__ == '__main__':
    main()
```
  - Run the sample code with input the following command in the terminal.
```
    C:\\...\code> python myholistic.py
``` 
  <image src="../image/holistic.png" width="30%" height="30%"><br>
  - If you want to stop this program, press "Esc" key while the preview window is active.

## Challenge[Hands1]
 - Display the angle between the vertical upward direction and the direction pointed by the index finger as shown in the figure below.
 - In this program we need to calculate the angle using 2D vectors.<br>
    <image src="../image/angle.gif" width="30%" height="30%"><br>
    
## Challenge[Hands2]
 - Create an interactive simple game using the information from **hands** by referring to the sample code below.
### Sample code
```python
import cv2
import mediapipe as mp
import numpy as np
import time
import random
mp_drawing = mp.solutions.drawing_utils
mp_hands = mp.solutions.hands

device = 0 # camera device number

def getFrameNumber(start:float, fps:int):
    now = time.perf_counter() - start
    frame_now = int(now * 1000 / fps)

    return frame_now

def calcAngle(v1, v2):
    v1_n = np.linalg.norm(v1)
    v2_n = np.linalg.norm(v2)

    cos_theta = np.inner(v1, v2) / (v1_n * v2_n)

    return np.degrees(np.arccos(cos_theta))

# check the angle between the vertical upward direction and the direction pointed by the index finger
def checkAngle(image, landmarks):
    angle = 0
    cv2.putText(image, str(angle), (100, 200), cv2.FONT_HERSHEY_SIMPLEX, 3, (0,0,255),5)

    return angle
      
def main():
    # For webcam input:
    global device

    cap = cv2.VideoCapture(device)
    fps = cap.get(cv2.CAP_PROP_FPS)
    wt  = cap.get(cv2.CAP_PROP_FRAME_WIDTH)
    ht  = cap.get(cv2.CAP_PROP_FRAME_HEIGHT)

    print("Size:", ht, "x", wt, "/Fps: ", fps)

    start = time.perf_counter()
    frame_prv = -1

    drawing_spec = mp_drawing.DrawingSpec(thickness=1, circle_radius=1)
    cv2.namedWindow('MediaPipe Hands', cv2.WINDOW_NORMAL)

    flag = 0
    quest = random.randint(1, 359)
    msg1 = ""
    msg2 = ""
    
    with mp_hands.Hands(
        max_num_hands=1,
        min_detection_confidence=0.7) as hands:
        while cap.isOpened():
            frame_now=getFrameNumber(start, fps)
            if frame_now == frame_prv:
                continue
            frame_prv = frame_now

            ret, frame = cap.read()
            if not ret:
                print("Ignoring empty camera frame.")
                # If loading a video, use 'break' instead of 'continue'.
                continue

            # Flip the image horizontally for a later selfie-view display, and convert
            # the BGR image to RGB.
            frame = cv2.cvtColor(cv2.flip(frame, 1), cv2.COLOR_BGR2RGB)

            # To improve performance, optionally mark the image as not writeable to
            # pass by reference.
            frame.flags.writeable = False
            results = hands.process(frame)

            # Draw the index finger annotation on the image.
            frame.flags.writeable = True
            frame = cv2.cvtColor(frame, cv2.COLOR_RGB2BGR)

            # Display the message
            msg1 = "Point in the direction of " + str(quest) + " degrees"
            cv2.putText(frame, "Reset[r key], Exit[Esc key]", (100, 50), cv2.FONT_HERSHEY_COMPLEX_SMALL, 1, (0,0,0),1)
            cv2.putText(frame, msg1, (100, 100), cv2.FONT_HERSHEY_COMPLEX_SMALL, 1, (0,255,255),2)
            cv2.putText(frame, msg2, (100, 200), cv2.FONT_HERSHEY_COMPLEX_SMALL, 5, (0,0,255),2)
            if results.multi_hand_landmarks:
                for hand_landmarks in results.multi_hand_landmarks:
                    if flag == 0:
                        if checkAngle(frame, hand_landmarks) == quest:
                            flag = 1
                        
            cv2.imshow('MediaPipe Hands', frame)

            if flag == 1:
                msg2 = "ok!"
            if cv2.waitKey(1) & 0xFF == 114:
                flag = 0
                msg2 = ""
                quest = random.randint(1, 359)
            if cv2.waitKey(1) & 0xFF == 27:
                break
    cap.release()

if __name__ == '__main__':
    main()
```
 - In this sample code, 1 to 359 degrees are displayed randomly, and "OK!" is displayed when the user points to the same angle.
 - In the `checkAngle` function, the angle between the vertical upward direction and the index finger is calculated.
 - Complete the `calcAngle` function using the program created in Challenge [Hands1].

## Challenge[Hans3]
 - Make a simple game like [Exercise[Hands2]](./holistic.md#exercisehands2) using the information from **hands**.
      
## Challenge[Pose1]
 - Make a shape of "O" or "X" with your arm and display it on the screen according to the shape.<br>
    <image src="../image/pose_q3.gif" width="30%" height="30%">

## Challenge[Pose2]
 - Make a simple game like [Exercise[Hands2]](./holistic.md#challenge[hands2]) using the information from **pose**.

## Challenge[Face1]
 - Display the face direction randomly,  and show how many times you have turned your face to the direction.<br>
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
  
## Challenge[Face3]
 - Make a simple game like [Exercise[Hands2]](./holistic.md#exercisehands2) using the information from **face**.
    
---

[README](../README.md)
