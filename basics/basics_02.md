# Tutorials for video image processing

## Objectives
To process the video image in Python 3 with your camera device.

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
- A value of grobal variable "device" (line 11)  is the device numbers of the camera starting from 0. 
  - The value is set as a file name if you want to process the movie file.
- Open the video stream and get the propaties of the video image
    | code | comment |
    :--- | :---
    | cv2.VideoCapture(device) | Open the video stream | 
    | cv2.CAP_PROP_FPS | FPS (frame / sec) rate | 
    | cv2.CAP_PROP_FRAME_WIDTH | Frame width | 
    | cv2.CAP_PROP_FRAME_HEIGHT | Frame height | 

### Read a frame from video stream
- Read a frame form video steram
    | code | comment | 
    :--- | :---
    | cap.read() | 1st return value is a boolean value for whether was able to read a frame <br> 2nd return value is the list of the pixel values in a frame |
    - The read function is called by every loop in this sample code, independent of the FPS. 
    - The loop time is costed the sum of the processing time with the read and any other functions in the while block and the sleep time with the waitKey function(1m sec).   
    - In the video stream, the returned frame from the function is the same as the previous frame when the loop time is shorter than 1/FPS sec.
    - In the movie file, the returned frame from the function is in order, independent of the loop time.
        | Device | The number of the returned frame |
        :--- | :--- 
        | camera | t + int( loop time / fps) |
        | movie file | t + 1 |
     
### Wait for the user's key input
- The "waitKey" function sleeps the process(thread) to wait for the user's key input during a value of the argument (m sec).
- It exits the while loop when the user presses the "Esc" key.
- "Esc" key is coded at 27.
- 
### Practice
- Set a value of the global variable "device" to adapt your PC environment.
- Run the sample code.
- Check the video window is came up, and the program is terminated with the "Esc" key press.

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

### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint (Sample of simple video-image processing)
- It's OK, you confirm only to be able to run the program.
- Which program, [video_viewer2.py](#video_viewer2py) or [video_viewer1.py](#video_viewer1py), is better to use is dependent on the situation.
- It's a more simple way for adapting the frame rate that a value of the argument in the "waitKey" function in the [video_viewer1.py](#video_viewer1py) is replaced to the time to the next frame is provided, like the following.
  ```python
  if cv2.waitKey(int(1000/fps)) & 0xFF == 27:
  ```

### Exercise (Selfie.py)
- Try to make "Let's selfie program"(selfie.py) by editing the [video_viewer1.py](#video_viewer1py) or the [video_viewer2.py](#video_viewer2py).
- Save the video frame to the still image file at that time when the user presses the "s" key.
    | Key | Details | 
    :---: | :---
    | q | The program is terminated. |
    | s | The video frame is saved as the still image. |

- Here is the hint code.  
  ```python
  key = cv2.waitKey(1)
  if key & 0xFF == ord('q'):
      break
  elif key & 0xFF == ord('s'):
      cv2.imshow("video", frame)
      cv2.imwrite("selfie.jpg", frame)
  ```
  - Write a still image 

    | code | comment |
    :--- | :---
    | cv2.imwrite("name", variable) | 1.1st argument is the file name(path) of the image which is saved. <br>2.2nd argument is the variable of the image. | 

  - Compare the recieved key with the key which you want to detect.

    | code | comment |
    :--- | :---
    | ord('caracter') | It's changed the character value of the argument to the number of Unicode. |

- If your program is correct, you will be able to find a jpeg file named "selfie.jpg" in the current folder when you press the "s" key.  
