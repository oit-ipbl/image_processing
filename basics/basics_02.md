# Image processing basics (2)

## Objectives
- This page explains how to process the video image in Python 3 with your camera device.
- This page explains how to detect the face/facial landmarks with OpenCV.
- This page explains how to process the face/facial landmarks detection on the video image.

## Prerequisite
- Open the VS Code by the running the `vscode.bat`. Confirm that the current directory shown in the terminal window is `code`.
- The python program (.py) has to be made in `code` folder. And all image files are saved (downloaded) in `imgs` folder and read from there. 
- You can run a python program with the input of the following command in the terminal.
    ```
    C:\\...\code> python XXX.py
    ```
## Sample of simple video-image processing

### video_viewer1.py
```python
# Sample of "Simple video-image processing"
# -*- coding: utf-8 -*-
import cv2

device = 0 # camera device number

# main----------------------------------------------------
def main():
    global device

    cap = cv2.VideoCapture(device)
    fps = cap.get(cv2.CAP_PROP_FPS)
    wt  = cap.get(cv2.CAP_PROP_FRAME_WIDTH)
    ht  = cap.get(cv2.CAP_PROP_FRAME_HEIGHT)

    print("Size:", ht, "x", wt, "/Fps: ", fps)
    while cap.isOpened() :
        ret, frame = cap.read()

        if cv2.waitKey(1) & 0xFF == 27:
            break
        cv2.imshow("video", frame)

    cv2.destroyAllWindows()
    cap.release()

# run-----------------------------------------------------
if __name__ == '__main__':
    main()
```

### Video propaties
- A value of grobal variable `device` in `video_viewer1.py` is the device number of the camera starting from 0. 
  - It can be read the movie file when a value of the `device` is set a movie file name, like the following.
  ```python
  device = "moviefile.avi"
  ```
- The following codes in `video_viewer1.py` are in order to open the video stream and get the propaties of the video-image. 
    | code | comment |
    :--- | :---
    | cv2.VideoCapture(device) | Open the video stream | 
    | cv2.CAP_PROP_FPS | FPS (frame / sec) rate | 
    | cv2.CAP_PROP_FRAME_WIDTH | Frame width | 
    | cv2.CAP_PROP_FRAME_HEIGHT | Frame height | 

### Read a frame from video stream
- The following function in `video_viewer1.py` is in order to read a frame form video steram.
    | code | comment | 
    :--- | :---
    | cap.read() | 1st return value is a boolean value for whether success in reaing a frame <br> 2nd return value is the list of the pixel values in a frame |
    - `cap.read()` function is called by every loop in `video_viewer1.py`, independent of the FPS. 
    - The time of the loop is costed the sum of the processing time with `cap.read()` and any other functions in the while block and the sleep time with `cv2.waitKey()` function(1m sec).   
        ```python
            while cap.isOpened() :
                ret, frame = cap.read()
                if cv2.waitKey(1) & 0xFF == 27:
                    break
                cv2.imshow("video", frame)
        ```
- In the video stream, the returned frame from `cap.read()` is the same as the previous frame when the time of the `cap.read()` function calling is shorter than 1/FPS sec.
- In the movie file, the returned frame from `cap.read()` function is in order, independent on the time of function calling.
    | Device | The number of the returned frame |
    :--- | :--- 
    | camera | t + int( time of the "read" function calling / fps) |
    | movie file | t + 1 |
     
### Wait for the user's key input
- `cv2.waitKey()` function sleeps the process(thread) in order to wait for the user's key input during a value of the argument (m sec).
- It exits the while loop when the user presses the `Esc` key.
- `Esc` key is coded at 27.

### Practice
- You should be copy [`video_viewer1.py`](#video_viewer1py) with the `clipboard` button and paste it to the VS Code, and save it as  `video_viewer1.py` in the `code` folder.
- If there is only one camera on your device, including the built-in, the number of your camera device is 0.
<!-- - You can check which device number that the camera is connected is with running the following program (`camera_detect.py`).
    ```python
    import cv2

    for i in range(0, 10): 
        cap = cv2.VideoCapture(i)
        if cap.isOpened(): 
            print("VideoCapture(", i, ") : Found")
            cap.release() 
        else:
            print("VideoCapture(", i, ") : None")
    cap.release() 
    ```
    - It can be ignored if a warning message like the following will appear.
         ```
         [ WARN:0] global C:\Users\appveyor\AppData\Local\Temp\1\pip-req-build-sxpsnzt6\opencv\modules\videoio\src\cap_msmf.cpp (435) `anonymous-namespace'::SourceReaderCB::~SourceReaderCB terminating async callback
         ```
    - It costs a few minutes to be run the program depending on the environment of your device. -->
- Set a value of the global variable `device` to adapt your PC environment.
- Run the sample code.
    - It can be ignored if a warning message like the following will appear.
         ```
         [ WARN:0] global C:\Users\appveyor\AppData\Local\Temp\1\pip-req-build-sxpsnzt6\opencv\modules\videoio\src\cap_msmf.cpp (435) `anonymous-namespace'::SourceReaderCB::~SourceReaderCB terminating async callback
         ```
- Check the video window is came up, and the program is terminated with the `Esc` key press.

### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint (Sample of simple video-image processing)
- It's OK, you can show the video image with your camera device.


## Sample of video-image processing adapted the frame rate

### Add the function to calculate the frame number from the processing time
- Define the function for the frame number calculation in order to avoid to be called `cap.read()` function multiple times, independent on the FPS.
- The processes after `cap.read()` function in the while block are skipped and continued the iteration if the next iteration is came up before the next frame is provided from the video stream.

### video_viewer2.py
```python
# Sample of video-image processing adapted the frame rate
# -*- coding: utf-8 -*-
import cv2
import time

device = 0 # camera device number

# added function -----------------------------------------
def getFrameNumber(start:float, fps:int):
    now = time.perf_counter() - start
    frame_now = int(now * 1000 / fps)

    return frame_now

# main----------------------------------------------------
def main():
    global device

    cap = cv2.VideoCapture(device)
    fps = cap.get(cv2.CAP_PROP_FPS)
    wt  = cap.get(cv2.CAP_PROP_FRAME_WIDTH)
    ht  = cap.get(cv2.CAP_PROP_FRAME_HEIGHT)

    print("Size:", ht, "x", wt, "/Fps: ", fps)

    start = time.perf_counter()
    frame_prv = -1
    while cap.isOpened() :
        frame_now=getFrameNumber(start, fps)
        if frame_now == frame_prv:
            continue
        frame_prv = frame_now

        ret, frame = cap.read()

        if cv2.waitKey(1) & 0xFF == 27:
            break
        cv2.imshow("video", frame)

    cv2.destroyAllWindows()
    cap.release()

# run-----------------------------------------------------
if __name__ == '__main__':
    main()
```

### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint (Sample of video-image processing adapted the frame rate)
- It's OK, you confirm only to be able to run the program.
- Which program, [`video_viewer2.py`](#video_viewer2py) or [`video_viewer1.py`](#video_viewer1py), is better to use is dependent on the situation.
- There is a more simple way for adapting the frame rate that a value of the argument in `cv2.waitKey()` function in the [`video_viewer1.py`](#video_viewer1py) is replaced to the time until the next frame is provided, like the following.
  ```python
  if cv2.waitKey(int(1000/fps)) & 0xFF == 27:
  ```

### Exercise (selfie.py)
- Try to make "Let's selfie program" (`selfie.py`) by modifying the [`video_viewer1.py`](#video_viewer1py) or the [`video_viewer2.py`](#video_viewer2py).
- Save a video frame to a still image file at the time when the user presses `s` key.
    | Key | Details | 
    :---: | :---
    | Esc | The program is terminated. |
    | s | The video frame is saved as a still image. |

- Here is the hint code.  
  ```python
  key = cv2.waitKey(1)
  if key & 0xFF == 27:
      break
  elif key & 0xFF == ord('s'):
      cv2.imshow("video", frame)
      cv2.imwrite("./imgs/selfie.jpg", frame)
  ```
  - The following function in the hint code is in order to write a still image. 

    | code | comment |
    :--- | :---
    | cv2.imwrite("name", variable) | 1st argument is the file name(path) of the image which is saved. <br>2nd argument is the variable of the image. | 

  - The following function in the hint code is in order to compare a received key with a key that you want to detect.

    | code | comment |
    :--- | :---
    | ord('a caracter') | It's changed a character in the argument to the number of Unicode. |

- If your program is correct, you will be able to find a jpeg file named `selfie.jpg` in `imgs` folder when you press `s` key.

### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint (Exercise (selfie.py))
- It's OK, if you can confirm that the `selfie.jpg` was saved correctly.

## Appendix: Sample of video-image recorder
### Preparation
- Download [`openh264-1.8.0-win64.dll`](https://oskit-my.sharepoint.com/:u:/g/personal/yoshiyuki_kamakura_oit_ac_jp/EbP0DohYsAtHt9sXCA22EZ8BR1LDrgoMHTeAv_ihERy_Kg?e=HVQlFv), and save it in `code` folder.
    - It's a H264 codec file for Windows.
<!--
### video_recorder.py
```python
# Sample of video-image recorder
# -*- coding: utf-8 -*-
import cv2

device = 0 # camera device number
video_name = "record.avi"

# main----------------------------------------------------
def main():
    global device, video_name
    recflag = False

    cap = cv2.VideoCapture(device)
    fps = cap.get(cv2.CAP_PROP_FPS)
    wt  = cap.get(cv2.CAP_PROP_FRAME_WIDTH)
    ht  = cap.get(cv2.CAP_PROP_FRAME_HEIGHT)

    print("Size:", ht, "x", wt, "/Fps: ", fps)

    writer = cv2.VideoWriter(video_name, cv2.VideoWriter_fourcc(*"H264"), fps, (int(wt), int(ht)))

    while cap.isOpened() :
        ret, frame = cap.read()
        if recflag:
            writer.write(frame)
            cv2.circle(frame, (20, 20), 5, [0,0,255], -1)


        key = cv2.waitKey(1)
        if key == 27:
            break
        elif key == ord('r'):
            recflag = True
        elif key == ord('q'):
            recflag = False

        cv2.imshow("video", frame)

    if writer.isOpened():
        writer.release()
    cv2.destroyAllWindows()
    cap.release()

# run-----------------------------------------------------
if __name__ == '__main__':
    main()
```
- The following line in `video_recorder.py` is in order to open the VideoWriter for preparing the video-image recording.
    ```python
    writer = cv2.VideoWriter(video_name, cv2.VideoWriter_fourcc(*"H264"), fps, (int(wt), int(ht)))
    ```
    - `cv2.VideoWriter_fourcc(*"H264")` specifies the codec of the recording. You can use the H264 codec if there is a codec file in the code folder.
    - You can see the arguments and more details of `VideoWriter()` [here](https://docs.opencv.org/3.4/dd/d9e/classcv_1_1VideoWriter.html)
- It can be started to record the video-image to `video_name` file when the user presses `r` key. And its recording is stopped when the user presses presses `q` key. It's implemented with the value in `recflag` flag.
- The following line in `video_recorder.py` is in order to write the frame on the file.
    ```python
    writer.write(frame)
    ```
- The following line in `video_recorder.py` is in order to close the writer before the program is finished. 
    ```python
    writer.release()
    ```
- You can find `video_name` in the `code` folder. 
-->
## Sample of face/facial landmarks detection 

### Preparation
- Download [trainedfile.zip](https://oskit-my.sharepoint.com/:u:/g/personal/yoshiyuki_kamakura_oit_ac_jp/EQzYzqnBcFdGvyDWooZESeIBGTP8bVaCJNiOQvCnTBGuKg?e=bmXtHn) and unzip the ZIP files. 
- `haarcascade_frontalface_default.xml` should be saved in `code` folder.
    - It's a trained dataset file that is provieded by [OpenCV](https://github.com/opencv) to detect the faces with the Haarcascade.
- `lbfmodel.ymal` should be saved in `code` folder.
    - It's a trained dateset file that is provieded by [kurnianggoro](https://github.com/kurnianggoro) to detect the facial landmarks with the LBF(Local binary fitting) model.

### cvface_detection.py
```python
# Sample of face detection 
# -*- coding: utf-8 -*-
import cv2

# main--------------------------------------------------------------------------------------
def main():  
    cascade = cv2.CascadeClassifier("./haarcascade_frontalface_default.xml")

    img = cv2.imread('./imgs/lena.jpg')
    faces = cascade.detectMultiScale(img)

    for i, face in enumerate(faces):
        fx, fy, fw, fh = face
        cv2.rectangle(img, (fx, fy), (fx+fw, fy+fh), [0,0,255], 1)

    cv2.imshow("face", img)
    
    cv2.waitKey(0)
    cv2.destroyAllWindows()

# run---------------------------------------------------------------------------------------
if __name__ == '__main__':
    main()
```

### face detection propaties
- It's a face detection program using Haar Cascade.
    - You can see the theory of Haar Cascade [here](https://docs.opencv.org/4.5.2/db/d28/tutorial_cascade_classifier.html). 
    - The following line is in order to prepare the face detection by reading a trained dataset `haarcascade_frontalface_default.xml`.
        ```python
        cascade = cv2.CascadeClassifier("./haarcascade_frontalface_default.xml")
        ```
    - The following line is in order to detect the faces in the image which is set in the argument.
        ```python
        faces = cascade.detectMultiScale(img)
        ```
    - The returned value of `cascade.detectMultiScale(img)` is an array which is listed the detected faces.
    - `faces` array consists of the elements which have the parameters of each detected face area.
    - The values of each element in `faces` array are consist of the x and y coordinates of upper-left, the width and height, for the area of the detected face. <br>
         ```
         face[i] = [x, y, width, height]
         faces = [face[0], face[1], face[2], ...]
         ```
    
    - You can see the arguments and more details of `cv2.CascadeClassifier()` and `detectMultiScale()` [here](https://docs.opencv.org/master/d1/de5/classcv_1_1CascadeClassifier.html).

### Tips on OpenCV
- The following line in `cvface_detection.py` is to be able to extract the element (`face`) in order from `faces` array along with the index number (`i`).
    ```python
    for i, face in enumerate(faces):
    ```
    - It can extract only each element (`face`) if the for loop is written without `enumerate()` function.
        ```python
        for face in faces:
        ```
    
- The following line in `cvface_detection.py` is in order to draw the rectangle on the image.
    ```python
    cv2.rectangle(img, (fx, fy), (fx+fw, fy+fh), [0,0,255], 1)
    ```
    | argument | comment |
    :--- | :---
    | img | The image to draw the rectangle. |
    | (fx, fy) | The x and y coordinates of the upper-left corner of the rectangle. |
    | (fx+fw, fy+fh) | The x and y coordinates of the lower-right corner of the rectangle. |
    | \[0,0,255\] | The line color of the rectangle. It's represented by the values of BGR color components. |
    | 1 | The line width of the rectangle. The rectangle is filled in with the color if its value sets -1. |

### Practice
- You should be copy [`cvface_detection.py`](#cvface_detectionpy) with the `clipboard` button and paste it to the VS Code, and save it as `cvface_detection.py` in the `code` folder.
- Check it can detect the face on Lenna's face.<br>
        ![result](../image/dflenna.jpg)
- The program is terminated with any key press.


### cvfacemark_detection.py
```python
# Sample of facial landmarks detection 
# -*- coding: utf-8 -*-
import cv2

# main--------------------------------------------------------------------------------------
def main():  
    cascade = cv2.CascadeClassifier("./haarcascade_frontalface_default.xml")
    lbf = cv2.face.createFacemarkLBF()
    lbf.loadModel("lbfmodel.yaml")

    img = cv2.imread('./img/lena.jpg')
    faces = cascade.detectMultiScale(img)
    
    for i,face in enumerate(faces):
        fx, fy, fw, fh = face
        cv2.rectangle(img, (fx, fy), (fx+fw, fy+fh), [0,0,255], 1)
        landmarks = lbf.fit(imgs, faces)
        _, list = landmarks

        for x, y in list[i][0]:
            cv2.circle(img, (x, y), 2,(0,255, 0), -1)

    cv2.imshow("face+facemark", img)

    cv2.waitKey(0)
    cv2.destroyAllWindows()

# run---------------------------------------------------------------------------------------
if __name__ == '__main__':
    main()
```

### facial landmarks detection propaties
- It's a facial landmarks detection program using LBF(Local binary fitting).
    - You can see the theory of LBF [here](https://github.com/kurnianggoro). 
    - The following line in `cvfacemark_detection.py` is in order to prepare to use the LBF model, and to read a trained dataset `lbfmodel.yaml`.
        ```python
        lbf = cv2.face.createFacemarkLBF()
        lbf.loadModel("lbfmodel.yaml")
        ```
    - The following line in `cvfacemark_detection.py` is in order to detect the facial landmarks around the face area in the image, which are set in the argument.
        ```python
        landmarks = lbf.fit(imgs, faces)
        ```
    - The following line in `cvfacemark_detection.py` is in order to extract the coordinates of the landmarks on each face as the list.
        ```python
        _, list = landmarks
        ```
    - It's defined that 68 facial landmarks are mapped on the face like [here](https://www.researchgate.net/figure/The-68-specific-human-face-landmarks_fig4_331769278).
    - You can extract all landmarks on each face like the following.
        ```python
        for x, y in list[i][0]:
        ```
    - You can extract the specific landmark like the following. It's an example to extract the No.10.
        ```python
        x, y = list[i][0][10]
        ```
        
### Tips on OpenCV
    
- The following line in `cvfacemark_detection.py` is in order to draw the circle on the image.
    ```python
    cv2.circle(img, (x, y), 5,(255,255, 0), -1)
    ```
    | argument | comment |
    :--- | :---
    | img | The image to draw the circle. |
    | (x, f) | The x and y coordinates of the center of the circle. |
    | 5 | The radius of the circle. |
    | (255, 255, 0) | The line color of the circle. It's represented by the values of BGR color components. |
    | -1 | The line width of the circle. The circle is filled in with the color if its value sets -1. |

### Practice
- You should be copy [`cvfacemark_detection.py`](#cvfacemark_detectionpy) with the `clipboard` button and paste it to the VS Code, and save it as `cvfacemark_detection.py` in the `code` folder.
- Check it can detect the face and facial landmarks on Lenna's face.<br>
    ![result](../image/dfmlenna.jpg)
- The program is terminated with any key press.

### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint (Sample of face/facial landmarks detection)
- It's OK, you can run the program of face/facial landmarks detection.

## Sample of the face/facial landmarks detection on the video-image

### cvfacemark_video.py

```python
# -*- coding: utf-8 -*-
import cv2
import time

device = 0 # camera device number

# added function -----------------------------------------
def getFrameNumber(start:float, fps:int):
    now = time.perf_counter() - start
    frame_now = int(now * 1000 / fps)

    return frame_now

# main--------------------------------------------------------------------------------------
def main():  
    global device

    cascade = cv2.CascadeClassifier("./haarcascade_frontalface_default.xml")
    lbf = cv2.face.createFacemarkLBF()
    lbf.loadModel("lbfmodel.yaml")

    cap = cv2.VideoCapture(device)
    fps = cap.get(cv2.CAP_PROP_FPS)
    wt  = cap.get(cv2.CAP_PROP_FRAME_WIDTH)
    ht  = cap.get(cv2.CAP_PROP_FRAME_HEIGHT)

    print("Size:", ht, "x", wt, "/Fps: ", fps)

    start = time.perf_counter()
    frame_prv = -1
    while cap.isOpened() :
        frame_now=getFrameNumber(start, fps)
        if frame_now == frame_prv:
            continue
        frame_prv = frame_now

        ret, frame = cap.read()

        faces = cascade.detectMultiScale(frame)
    
        if len(faces)>0:
            face = faces[0]
            fx, fy, fw, fh = face
            cv2.rectangle(frame, (fx, fy), (fx+fw, fy+fh), [0,0,255], 0)
            landmarks = lbf.fit(frame, faces)
            _, list = landmarks

            for x, y in list[0][0]:
                cv2.circle(frame, (x, y), 2,(0,255, 0), -1)

        if cv2.waitKey(1) & 0xFF == 27:
            break
        cv2.imshow("video", frame)

    cv2.destroyAllWindows()
    cap.release()

# run---------------------------------------------------------------------------------------
if __name__ == '__main__':
    main()
```
- This program is combined [`video_viewer2.py`](#video_viewer2py) with [`cvfacemark_detection.py`](#cvfacemark_detectionpy).
- This program is changed to be able to detect only one person in the video. You can check that in the following.
    - face detection for one perspn
        ```python
        if len(faces)>0:
            face = faces[0]
            fx, fy, fw, fh = face
        ```
    - facial landmarks detection for one perspn
        ```python
        for x, y in list[0][0]:
        ``` 
- Sometimes, the results of the detections not good because the face detection time with haar cascade would be longer than the FPS.

### Practice
- You should be copy [`cvfacemark_video.py`](#cvfacemark_videopy) with the `clipboard` button and paste it to the VS Code, and save it as `cvfacemark_video.py` in the `code` folder.
- Check it can detect the face and facial landmarks on the face in the video-image.
- The program is terminated with `Esc` key press.

### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint (Sample of the face/facial landmarks detection on the video-image)
- It's OK, you can run the program of face/facial landmarks detection on the video-image.

---
[README](../README.md)

