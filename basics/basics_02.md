# Tutorials for video image processing

- Grobal dariable "device" (line 11)  means the device numbers of the camera starting from 0. 
  - It can be specified the file name if you want to process the movie file.
- 

```python
# -*- coding: utf-8 -*-
import cv2
import numpy as np

device = 0 # number of camera device

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
