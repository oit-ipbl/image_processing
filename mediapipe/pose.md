# Tutorials for MediaPipe Pose

## Objectives
This page explains how to make a program for pose detection and get information.

## Prerequisite
You have to finish [MediaPipe Hands](../mediapipe/hands.md).

## Pose landmark model
By using [MediaPipe](https://google.github.io/mediapipe/), we can obtain 3D position information of 33 landmarks as shown by the red marker in the following figure.<br>
<image src="../image/pose_tracking_full_body_landmarks.png" width="75%" height="75%"><br>

## Practice[Display all pose landmarks]
  Get pose landmarks and display them.
  - Execute "vscode.bat" file, and open the VSCode.
  - Make a python file `mypose.py`. 
  - Type the following sample code. It's OK copy and paste.

### Sample code
```python
import cv2
import mediapipe as mp
import time
mp_drawing = mp.solutions.drawing_utils
mp_pose = mp.solutions.pose

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

    pose = mp_pose.Pose(
        min_detection_confidence=0.5,
        min_tracking_confidence=0.5)

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
        results = pose.process(frame)

        # Draw the pose annotation on the image.
        frame.flags.writeable = True
        frame = cv2.cvtColor(frame, cv2.COLOR_RGB2BGR)
        if results.pose_landmarks:
            mp_drawing.draw_landmarks(frame, results.pose_landmarks, mp_pose.POSE_CONNECTIONS)
                
        cv2.imshow('MediaPipe Pose', frame)
        if cv2.waitKey(5) & 0xFF == 27:
            break
    cap.release()

if __name__ == '__main__':
    main()
```
  - Run the sample code with input the following command in the terminal.
```
    C:\\...\code> python mypose.py
``` 
  <image src="../image/pose.png" width="30%" height="30%"><br>
  - If you want to stop this program, press "Esc" key while the preview window is active.

### How to refer to landmark information
 - `landmark.x` and `landmark.y` are normalized to [0.0-1.0].
 - `landmark.z` represents the landmark depth with the depth at the midpoint of hips being the origin, and the smaller the value the closer the landmark is to the camera.

## Exercise[Pose1]
 - Calculate and display the center of gravity from all the obtained landmarks.
 - Define a function that calculates and displays the center of gravity with reference to [MediaPipe (1)](../mediapipe/hands.md)<br>
    <image src="../image/pose_center.png" width="30%" height="30%"><br>

### :o:Checkpoint
It's OK, you can finish the Exercise[Pose1].

---

[README](../README.md)
