# Tutorials for MediaPipe Hands

## Objectives
This page explains how to make a program for palm detection and get information.

## Prerequisite
You have to finish [Image processing basics (1)](../basics/basics_01.md), [Image processing basics (2)](../basics/basics_02.md).

## Hand landmark model
By using [MediaPipe](https://google.github.io/mediapipe/), we can obtain 3D position information of 21 landmarks as shown by the red marker in the following figure.<br>
<image src="../image/hand_landmarks.png" width="75%" height="75%"><br>

## Practice[Get information of arbitrary landmark]
  Get information and display about an landmark of index finger.
  - Execute "vscode.bat" file, and open the VSCode.
  - Click the following button, and make a python file `myhands.py` in the `code` directory. <br><!--場所を書いたほうが良いかなと思いいました-->
  <image src="../image/newfile2.png" width="50%" height="50%"><br>
  - Type the following sample code. It's OK copy and paste.
<!-- templateとsample codeでずれがあると分かりにくいかもしれません-->
<!-- 以下のプログラムの cap = cv2.VideoCapture(0) のところですが，数字を変えても外付けのWebカメラでは認識できませんでした．PC内蔵カメラやバーチャルカメラは適当に数字をあげていくといけたようなんですが．．どこかでカメラ番号を確認ってできないものでしょうか？ -->
### Sample code
```python
import cv2
import mediapipe as mp
mp_drawing = mp.solutions.drawing_utils
mp_hands = mp.solutions.hands

def main():
    # For webcam input:
    cap = cv2.VideoCapture(0)
    cv2.namedWindow('MediaPipe Hands', cv2.WINDOW_NORMAL)
    with mp_hands.Hands(
        min_detection_confidence=0.5,
        min_tracking_confidence=0.5) as hands:
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
            results = hands.process(image)

            # Draw the index finger annotation on the image.
            image.flags.writeable = True
            image = cv2.cvtColor(image, cv2.COLOR_RGB2BGR)
            if results.multi_hand_landmarks:
                for hand_landmarks in results.multi_hand_landmarks:
                    draw_fingertip_landmarks(image, hand_landmarks)
            cv2.imshow('MediaPipe Hands', image)
            if cv2.waitKey(5) & 0xFF == 27:
                break
    cap.release()

# Draw a circle on index finger
def draw_fingertip_landmarks(image, landmarks):
    image_width, image_height = image.shape[1], image.shape[0]
    landmark_point = []

    for index, landmark in enumerate(landmarks.landmark):
        if landmark.visibility < 0 or landmark.presence < 0:
            continue

        # Convert the obtained landmark values x and y to the coordinates on the image
        landmark_x = min(int(landmark.x * image_width), image_width - 1)
        landmark_y = min(int(landmark.y * image_height), image_height - 1)
        landmark_z = landmark.z

        landmark_point.append([landmark_x, landmark_y, landmark_z])

    # Draw a circle on index finger and display the coordinate value
    cv2.circle(image, (landmark_point[8][0], landmark_point[8][1]), 7, (0, 0, 255), 3)
    cv2.putText(image, "(" + str(landmark_point[8][0]) + ", " + str(landmark_point[8][1]) + ")", 
        (landmark_point[8][0] - 20, landmark_point[8][1] - 20), cv2.FONT_HERSHEY_SIMPLEX, 1, (0,0,255), 2)

if __name__ == '__main__':
    main()
```
  - Execute "myhands.py" by clicking the execution button.<br>
  <image src="../image/index_finger_text.png" width="30%" height="30%"><br>
  - If you want to stop this program, press "Esc" key while the preview window is active.
    
### How to refer to landmark information
 - In the above Sample code, the detection results of all hands are stored in the variable `results`.
```python
    results = hands.process(image)
```
 - All the obtained landmark information is stored in the variable `results`. 
    You can refer to the landmark information of a specific hand as follows. 
    In the following example, the coordinate value of landmark #4(thumb) of the 0th hand is obtained.
```python
    results.multi_hand_landmarks[0].landmark[4]
```
 - Since the obtained values(`landmark.x`, `landmark.y`) are normalized to [0.0-1.0], it is necessary to convert them to the coordinates of the image as follows.
 - `landmark.z` represents the landmark depth with the depth at the wrist being the origin, and the smaller the value the closer the landmark is to the camera.
```python
    landmark_x = min(int(landmark.x * image_width), image_width - 1)
    landmark_y = min(int(landmark.y * image_height), image_height - 1)
```
 - In the `draw_fingertip_landmarks` function, the 3D coordinates of each landmark are stored in the list `landmark_point`. 
    The 3D coordinates of the index finger are stored in `landmark_point[8]`, the x-coordinate is stored in `landmark_point[8][0]`, the y-coordinate is stored in `landmark_point[8][1]`, and the z-doordinate is stored in `landmark_point[8][2]`. The number 8 is based on the hand landmark model. <!--8がどこからでてきたのかなと思いました-->
```python
    cv2.circle(image, (landmark_point[8][0], landmark_point[8][1]), 7, (0, 0, 255), 3)
    cv2.putText(image, "(" + str(landmark_point[8][0]) + ", " + str(landmark_point[8][1]) + ")", 
        (landmark_point[8][0] - 20, landmark_point[8][1] - 20), cv2.FONT_HERSHEY_SIMPLEX, 1, (0,0,255), 2)
````
  - `landmark.visibility` indicates whether landmark is visible or occluded by other objects. `landmark.presence` indicates whether landmark is present on the scene.
    Both value ranges are [0.0-1.0].
````python
    if landmark.visibility < 0 or landmark.presence < 0:
        continue
````
    
## Exercise[Hand1]
 - Draw red circles on all fingertips.<br>
    <image src="../image/q1_fingertips.png" width="30%" height="30%"><br>

## Exercise[Hand2]
 - Calculate the center of gravity of fingertips, and draw green circle.<br>
    <image src="../image/q2_fingertips_c.png" width="30%" height="30%"><br>
### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint
It's OK, you can finish the Exercise[Hand1] and [Hand2].
<!-- ここのCheckpointは1つにまとめちゃって良いかと思います-->

## Practice[Calculate the angle between two vectors]
 - To recognize the shape of the finger, it is necessary to calculate the angle between the vectors.
  - Make a python file `calc2vec.py`. 
  - Type the following sample code. It's OK copy and paste.
  <!--他にも何か所かありました-->

### Sample code
````python
import numpy as np

def main():
    vec1 = np.array([1, 1, 1])
    vec2 = np.array([1, 1, 0])

    print(v_angle3d(vec1, vec2))
    
def v_angle3d(v1, v2):
    v1_n = np.linalg.norm(v1)
    v2_n = np.linalg.norm(v2)

    cos_theta = np.inner(v1, v2) / (v1_n * v2_n)

    return np.rad2deg(np.arccos(cos_theta))

if __name__ == '__main__':
    main()
````
 - Execute "calc2vec.py" by clicking the execution button.
 - In this Sample code, calculate the angle between vec1 and vec2. The `v_angle` function takes two vectors as input and returns the angle(degree) between the two vectors.

## Practice[Judgement of whether the finger is bend or not]
<!--not bent．他にもあったような気がします．-->
 - Judge whether the index finger is open by using the 3D coordinates of the landmark.
  - Make a python file `OpenFinger.py`. 
  - Type the following template. It's OK copy and paste.

### Sample code
```python
import cv2
import mediapipe as mp
import numpy as np
mp_drawing = mp.solutions.drawing_utils
mp_hands = mp.solutions.hands

def main():
    # For webcam input:
    cap = cv2.VideoCapture(0)
    cv2.namedWindow('MediaPipe Hands', cv2.WINDOW_NORMAL)
    with mp_hands.Hands(
        max_num_hands=1,
        min_detection_confidence=0.7) as hands:
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
            results = hands.process(image)

            # Draw the index finger annotation on the image.
            image.flags.writeable = True
            image = cv2.cvtColor(image, cv2.COLOR_RGB2BGR)

            if results.multi_hand_landmarks:
                for hand_landmarks in results.multi_hand_landmarks:
                    check_open_finger(image, hand_landmarks)

            cv2.imshow('MediaPipe Hands', image)

            if cv2.waitKey(5) & 0xFF == 27:
                break
    cap.release()

# check the index finger
def check_open_finger(image, landmarks):
    image_width, image_height = image.shape[1], image.shape[0]
    landmark_point = []

    for index, landmark in enumerate(landmarks.landmark):
        if landmark.visibility < 0 or landmark.presence < 0:
            continue

        # Convert the obtained landmark values x and y to the coordinates on the image
        landmark_x = min(int(landmark.x * image_width), image_width - 1)
        landmark_y = min(int(landmark.y * image_height), image_height - 1)
        landmark_z = landmark.z

        landmark_point.append([landmark_x, landmark_y, landmark_z])

        if len(landmark_point) != 0 and len(landmark_point)==21:
            vec1 = (landmark_point[5][0] - landmark_point[6][0], 
            landmark_point[5][1] - landmark_point[6][1], 
            landmark_point[5][2] - landmark_point[6][2])
            vec2 = (landmark_point[7][0] - landmark_point[6][0], 
            landmark_point[7][1] - landmark_point[6][1], 
            landmark_point[7][2] - landmark_point[6][2])
            if v_angle3d(vec1, vec2) > 140:
                cv2.putText(image, "open", (100, 100), cv2.FONT_HERSHEY_SIMPLEX, 3, (0,0,255),5)
            else:
                cv2.putText(image, "bend", (100, 100), cv2.FONT_HERSHEY_SIMPLEX, 3, (0,0,255),5)

            cv2.circle(image, (landmark_point[5][0], landmark_point[5][1]), 7, (0, 0, 255), 3)
            cv2.circle(image, (landmark_point[6][0], landmark_point[6][1]), 7, (0, 0, 255), 3)
            cv2.circle(image, (landmark_point[7][0], landmark_point[7][1]), 7, (0, 0, 255), 3)
            cv2.line(image, (landmark_point[5][0], landmark_point[5][1]), (landmark_point[6][0], landmark_point[6][1]), (0, 255, 0))
            cv2.line(image, (landmark_point[6][0], landmark_point[6][1]), (landmark_point[7][0], landmark_point[7][1]), (0, 255, 0))

def v_angle3d(v1, v2):
    v1_n = np.linalg.norm(v1)
    v2_n = np.linalg.norm(v2)

    cos_theta = np.inner(v1, v2) / (v1_n * v2_n)

    return np.rad2deg(np.arccos(cos_theta))

if __name__ == '__main__':
    main()
````
  - Execute "OpenFinger.py" by clicking the execution button.<br>
  <image src="../image/open.png" width="30%" height="30%"> <image src="../image/close.png" width="30%" height="30%"><br>
  - If you want to stop this program, press "Esc" key while the preview window is active.                                                           

### How to judge whether your finger is bent or not
 - In this Sample code, the angle between the vectors obtained from the joint position of the finger is calculated and judged whether the finger is bent or not.
  - If the angle between vector `landmark_point[5] - landmark_point[6]` and vector `landmark_point[7] - landmark_point[6]` is greater than 140 degrees, display "open".<br>
    <image src="../image/vec.png" width="20%" height="20%"><br>
````python
    if v_angle3d(vec1, vec2) > 140:
        cv2.putText(image, "open", (100, 100), cv2.FONT_HERSHEY_SIMPLEX, 3, (0,0,255),5)
````

## Exercise[Hand3]
 - Count and display the number of fingers opened.
    - It is necessary to think about the judgment method with each finger.
    - To be robust to changes in hand orientation, we need to use 3D vectors to calculate the angle.<br>
    <image src="../image/num.gif" width="30%" height="30%"><br>
### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint
It's OK, you can finish the Exercise[Hand3].
      
## Challenge[Hand1]
 - Display the angle between the vertical upward direction and the direction pointed by the index finger as shown in the figure below.
 - In this program we need to calculate the angle using 2D vectors.<br>
    <image src="../image/angle.gif" width="30%" height="30%"><br>


---

[README](../README.md)
