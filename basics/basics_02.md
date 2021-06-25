# Tutorials for video image processing

## Purpose
To process the video image in Python 3 with your camera device.

## Sample of simple video-image processing

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

    print("fps: ", fps)
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

### Video status
- A value of grobal variable "device" (line 11)  is the device numbers of the camera starting from 0. 
  - The value is set as a file name if you want to process the movie file.
- 
