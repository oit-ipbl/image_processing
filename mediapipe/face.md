# Tutorials for MediaPipe Face

## Objectives
This page explains how to make a program for face detection and get information.

## Prerequisite
You have to finish [MediaPipe (2)](../mediapipe/pose.md).

## Face landmark model
By using [MediaPipe](https://google.github.io/mediapipe/), we can obtain 3D position information of 468 landmarks as shown by the red marker in the following figure.<br>
<image src="../image/face_landmark1.png" width="50%" height="50%"><br>

## Practice[Display all face landmarks]
  Get information and display about face landmarks.
  - Execute "vscode.bat" file, and open the VSCode.
  - Make a python file `myface.py`. 
  - Type the following template. It's OK copy and paste.

### Sample code
```python
import cv2
import mediapipe as mp
import numpy as np
mp_drawing = mp.solutions.drawing_utils
mp_face_mesh = mp.solutions.face_mesh

def main():
    # For webcam input:
    drawing_spec = mp_drawing.DrawingSpec(thickness=1, circle_radius=1)
    cap = cv2.VideoCapture(0)
    with mp_face_mesh.FaceMesh(
        min_detection_confidence=0.5,
        min_tracking_confidence=0.5) as face_mesh:
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
            results = face_mesh.process(image)

            # Draw the face mesh annotations on the image.
            image.flags.writeable = True
            image = cv2.cvtColor(image, cv2.COLOR_RGB2BGR)
            if results.multi_face_landmarks:
                for face_landmarks in results.multi_face_landmarks:
                    my_draw_face(image, face_landmarks)
            cv2.imshow('MediaPipe FaceMesh', image)
            if cv2.waitKey(5) & 0xFF == 27:
                break
        cap.release()

def my_draw_face(image, landmarks):
    image_width, image_height = image.shape[1], image.shape[0]
    landmark_point = []

    for index, landmark in enumerate(landmarks.landmark):
        if landmark.visibility < 0 or landmark.presence < 0:
            continue
        
        # Convert the obtained landmark values x and y to the coordinates on the image
        landmark_x = min(int(landmark.x * image_width), image_width - 1)
        landmark_y = min(int(landmark.y * image_height), image_height - 1)
        landmark_z = landmark.z

        landmark_point.append((index, landmark_x, landmark_y, landmark_z))

    if len(landmark_point) != 0:
        for i in range(0, len(landmark_point)):
            cv2.circle(image, (int(landmark_point[i][1]),int(landmark_point[i][2])), 1, (0, 255, 0), 1)


if __name__ == '__main__':
    main()
```
  - Execute "myface.py" by clicking the execution button.<br>
  <image src="../image/face.png" width="30%" height="30%"><br>
  - If you want to stop this program, press "Esc" key while the preview window is active.

### How to refer all the landmarks stored in the list
 - Draw by referring to all the landmarks stored in the list by the following code.
````python
    for i in range(0, len(landmark_point)):
        cv2.circle(image, (int(landmark_point[i][1]),int(landmark_point[i][2])), 1, (0, 255, 0), 1)
````

## Exercise[Face1]
 - Calculate the center of gravity of all face landmarks, and draw green circle.<br>
    <image src="../image/q1_face.png" width="30%" height="30%"><br>

### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint
It's OK, you can finish the Exercise[Face1].

## Exercise[Face2]
 - Display "left" or "right" according to the orientation of the face.<br>
    <image src="../image/q2_face.png" width="30%" height="30%"><br>

### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint
It's OK, you can finish the Exercise[Face2].
      
## Challenge[Face1]
 - Display "smile" when laughing. 
 - The position of facial parts is the basic information for classifying facial expressions.
 - Think so that you can judge correctly even if you tilt your face
途中
