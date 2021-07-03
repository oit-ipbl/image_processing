# Image processing basics (2)

## Objectives
- You can learn how to process the video image in Python 3 with your camera device.
- You can learn how to detect the face/facial landmarks with OpenCV.

## Prerequisite
- Open the VS Code by the running the `vscode.bat`. Confirm that the current directory shown in the terminal window is `code`.
- The python program (.py) has to be made in the `code` folder. And all image files are saved (downloaded) in the `img` folder and read from there. 
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
- A value of grobal variable `device` is the device numbers of the camera starting from 0. 
  - It can be read the movie file when it's set a value of the global variable `device` to a file name of the movie, like the following.
  ```python
  device = "moviefile.mp3"
  ```
- The following codes in `video_viewer1.py` are in order to Open the video stream and get the propaties of the video image. 
    | code | comment |
    :--- | :---
    | cv2.VideoCapture(device) | Open the video stream | 
    | cv2.CAP_PROP_FPS | FPS (frame / sec) rate | 
    | cv2.CAP_PROP_FRAME_WIDTH | Frame width | 
    | cv2.CAP_PROP_FRAME_HEIGHT | Frame height | 

### Read a frame from video stream
- The following function in `video_viewer1.py` are in order to read a frame form video steram.
    | code | comment | 
    :--- | :---
    | cap.read() | 1st return value is a boolean value for whether was able to read a frame <br> 2nd return value is the list of the pixel values in a frame |
    - The read function is called by every loop in `video_viewer1.py`, independent of the FPS. 
    - The looping time is costed the sum of the processing time with the `read()` and any other functions in the while block and the sleep time with the `waitKey()` function(1m sec).   
        ```python
            while cap.isOpened() :
                ret, frame = cap.read()
                if cv2.waitKey(1) & 0xFF == 27:
                    break
                cv2.imshow("video", frame)
        ```
- In the video stream, the returned frame from `cap.read()` is the same as the previous frame when the time of the `cap.read()` function calling is shorter than 1/FPS sec.
- In the movie file, the returned frame from `cap.read()` function is in order, independent of the loop time.
    | Device | The number of the returned frame |
    :--- | :--- 
    | camera | t + int( time of the "read" function calling / fps) |
    | movie file | t + 1 |
     
### Wait for the user's key input
- The `waitKey` function sleeps the process(thread) to wait for the user's key input during a value of the argument (m sec).
- It exits the while loop when the user presses the `Esc` key.
- `Esc` key is coded at 27.

### Practice
- You should be copy [`video_viewer1.py`](#video_viewer1py) with the `clipboard` button and paste it to the VS Code, and save it as  `video_viewer1.py` in the `code` folder.
- If there is a camera on your device, including the built-in, the number of your camera device is 0.
- You can check which device number that the camera is connected is with running the following program (`camera_detect.py`).
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
    - It costs a few minutes to be run the program depending on the environment of your device.
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

### add the function to calculate the frame number from the processing time
- Define the function for the frame number calculation to keep out of the read function calling multiple times.
- The loop is continued without the frame reading process if the read function is called before the next frame is provided from the video stream.

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
- It's a more simple way for adapting the frame rate that a value of the argument in the `waitKey` function in the [`video_viewer1.py`](#video_viewer1py) is replaced to the time to the next frame is provided, like the following.
  ```python
  if cv2.waitKey(int(1000/fps)) & 0xFF == 27:
  ```

### Exercise (selfie.py)
- Try to make "Let's selfie program" (`selfie.py`) by modifying the [`video_viewer1.py`](#video_viewer1py) or the [`video_viewer2.py`](#video_viewer2py).
- Save the video frame to the still image file at that time when the user presses the `s` key.
    | Key | Details | 
    :---: | :---
    | Esc | The program is terminated. |
    | s | The video frame is saved as the still image. |

- Here is the hint code.  
  ```python
  key = cv2.waitKey(1)
  if key & 0xFF == 27:
      break
  elif key & 0xFF == ord('s'):
      cv2.imshow("video", frame)
      cv2.imwrite("./img/selfie.jpg", frame)
  ```
  - The following function in the hint code in order to write a still image. 

    | code | comment |
    :--- | :---
    | cv2.imwrite("name", variable) | 1st argument is the file name(path) of the image which is saved. <br>2nd argument is the variable of the image. | 

  - The following function in the hint code in order to compare a received key with a key that you want to detect.

    | code | comment |
    :--- | :---
    | ord('a caracter') | It's changed a character in the argument to the number of Unicode. |

- If your program is correct, you will be able to find a jpeg file named `selfie.jpg` in `img` folder when you press the `s` key.

### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint (Exercise (selfie.py))
- It's OK, if you can confirm that the `selfie.jpg` was saved correctly.

## Sample of face/facial landmarks detection 

### preparation
- Download [`haarcascade_frontalface_default.xml`]() which is provied by [OpenCV](https://github.com/opencv), and save it to `code` folder.
    - It's a trained dataset file to detect the faces with the Haarcascade.
- Download [`lbfmodel.ymal`]() which is provied by [kurnianggoro](https://github.com/kurnianggoro), and save it to `code` folder.
    - It's a trained dateset file to detect the facial landmarks with the LBF(Local binary fitting) model.

### cvface_detection.py
```python
# -*- coding: utf-8 -*-
import cv2

# main--------------------------------------------------------------------------------------
def main():  
    cascade = cv2.CascadeClassifier("./haarcascade_frontalface_default.xml")

    img = cv2.imread('./img/lena.jpg')
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
    - The following line is to prepare the face detection by reading a trained dataset file.
        ```python
        cascade = cv2.CascadeClassifier("./haarcascade_frontalface_default.xml")
        ```
    - The following line is to detect the faces in the image which is set in the argument.
        ```python
        faces = cascade.detectMultiScale(img)
        ```
    - You can see the arguments and more detiels of `cv2.CascadeClassifier()` and `detectMultiScale()` [here](https://docs.opencv.org/master/d1/de5/classcv_1_1CascadeClassifier.html).

### Practice
- You should be copy [`cvface_detection.py`](#cvface_detectionpy) with the `clipboard` button and paste it to the VS Code, and save it as `cvface_detection.py` in the `code` folder.
- Check it can detect the face on Lenna's face.<br>
        ![result](../image/dflenna.jpg)
- The program is terminated with any key press.


### cvfacemark_detection.py
```python
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
        landmarks = lbf.fit(img, faces)
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

### Practice
- You should be copy [`cvfacemark_detection.py`](#cvfacemark_detectionpy) with the `clipboard` button and paste it to the VS Code, and save it as `cvfacemark_detection.py` in the `code` folder.
- Check it can detect the face and facial landmarks on Lenna's face.<br>
    ![result](../image/dfmlenna.jpg)
- The program is terminated with any key press.

### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint (Sample of face/facial landmarks detection)
- It's OK, you can run the program of face/facial landmarks detection.
