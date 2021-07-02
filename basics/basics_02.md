# Tutorials for video image processing

## Objectives
To process the video image in Python 3 with your camera device.

## Sample of simple video-image processing

### Sample code
```python
# Sample of "Simple video-image processing"
# -*- coding: utf-8 -*-
import cv2
import numpy as np

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

        if cv2.waitKey(1) & 0xFF == ord('q'):
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
- It exits the while loop when the user inputs the "q" key.

### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint
- Set a value of the global variable "device" to adapt your PC environment.
- Run the sample program.
- Check the video window come up and quit the program with the "q" button press.
