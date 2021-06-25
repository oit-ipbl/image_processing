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

        if cv2.waitKey(int(1000/fps)) & 0xFF == ord('q'):
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

| Open the video stream | cv2.VideoCapture(device) |
| FPS (frame / sec) rate | cv2.CAP_PROP_FPS |
| Frame width | cv2.CAP_PROP_FRAME_WIDTH |
| Frame height | cv2.CAP_PROP_FRAME_HEIGHT |
 
