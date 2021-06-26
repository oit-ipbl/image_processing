# Tutorials for MediaPipe Hands

## Objectives
This page explains how to make a program for palm detection and get information.

## Hand landmark model
By using MediaPipe, we can obtain 3D position information of 21 landmarks as shown by the red marker in the following figure.<br>
<image src="../image/hand_landmarks.png" width="75%" height="75%"><br>

## Practice[Get information of arbitrary landmark]
  Get information and display about an landmark of index finger.
  - Execute "vscode.bat" file, and open the VSCode.
  - Click the following button, and make a python file `myhands.py`. <br>
  <image src="../image/newfile2.png" width="75%" height="75%"><br>
  - Type the following template. It's OK copy and paste.

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

        landmark_point.append((landmark_x, landmark_y))

    # Draw a circle on index finger and display the coordinate value
    cv2.circle(image, landmark_point[8], 7, (0, 0, 255), 3)
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
 - In the draw_fingertip_landmarks function, the 3D coordinates of each landmark are stored in the list `landmark_point`. 
    The 3D coordinates of the index finger are stored in `landmark_point[8]`, the x-coordinate is stored in `landmark_point[8][0]`, and the y-coordinate is stored in `landmark_point[8][1]`.
```python
    cv2.circle(image, landmark_point[8], 7, (0, 0, 255), 3)
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

### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint
It's OK, you can finish the Exercise[Hand1].
      
## Exercise[Hand2]
 - Calculate the center of gravity of fingertips, and draw green circle.<br>
    <image src="../image/q2_fingertips_c.png" width="30%" height="30%"><br>
### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint
It's OK, you can finish the Exercise[Hand2].

## Practice[Judgement of whether the finger is opened]
  Judge whether the index finger is open by using the 3D coordinates of the landmark.
  - Make a python file `OpenFinger.py`. 
  - Type the following template. It's OK copy and paste.

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
    tipids = [4,8,12,16,20]   #list of all landmarks of the tips of fingers

    for index, landmark in enumerate(landmarks.landmark):
        if landmark.visibility < 0 or landmark.presence < 0:
            continue

        # Convert the obtained landmark values x and y to the coordinates on the image
        landmark_x = min(int(landmark.x * image_width), image_width - 1)
        landmark_y = min(int(landmark.y * image_height), image_height - 1)

        landmark_point.append((index, landmark_x, landmark_y))

        if len(landmark_point) != 0 and len(landmark_point)==21:
            fingerlist=[]

            if landmark_point[8][2] < landmark_point[6][2]:
                fingerlist.append(1)
            else:
                fingerlist.append(0)
            
            if len(fingerlist)!=0:
                fingercount=fingerlist.count(1)

            cv2.putText(image, str(fingercount), (100, 100), cv2.FONT_HERSHEY_SIMPLEX, 3, (0,0,255),5)
            cv2.circle(image, (landmark_point[6][1], landmark_point[6][2]), 7, (0, 0, 255), 3)
            cv2.circle(image, (landmark_point[7][1], landmark_point[7][2]), 7, (0, 0, 255), 3)
            cv2.circle(image, (landmark_point[8][1], landmark_point[8][2]), 7, (0, 0, 255), 3)
            cv2.line(image, (landmark_point[6][1], landmark_point[6][2]), (landmark_point[7][1], landmark_point[7][2]), (0, 255, 0))
            cv2.line(image, (landmark_point[7][1], landmark_point[7][2]), (landmark_point[8][1], landmark_point[8][2]), (0, 255, 0))


if __name__ == '__main__':
    main()
````
  - Execute "OpenFinger.py" by clicking the execution button.<br>
  <image src="../image/open.png" width="30%" height="30%"> <image src="../image/close.png" width="30%" height="30%"><br>
  - If you want to stop this program, press "Esc" key while the preview window is active.                                                           

### How to judge whether the finger is opened
 - aaa.
    
## Challenge[Hand1]
 - Count and display the number of fingers opened.<br>
    <image src="../image/num.gif" width="30%" height="30%"><br>

途中
