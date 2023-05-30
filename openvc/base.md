# Opencv

## Simple image viewer
Copy and paste the following code as “opencv_sample_00.py” and execute it!
```python
import cv2

if __name__ == '__main__':
    WINNAME = "OpenCV Sample 00"
    cv2.namedWindow(WINNAME)
    img = cv2.imread('./image00.jpg')
    cv2.imshow(WINNAME, img)
    key = cv2.waitKey(0)
```

## Simple image manipulation
Copy and paste the following code as “opencv_sample_01.py” and execute it!

```python
from __future__ import print_function
import cv2
import numpy

WINNAME = "OpenCV Sample 01"
WIDTH = 640
HEIGHT = 480

def run():
    img = numpy.zeros((HEIGHT, WIDTH, 3))
    for r in range(256):
        for g in range(256):
            for b in range(256):
                img[...] = (b/255.0, g/255.0, r/255.0)
                print(img)
                cv2.imshow(WINNAME, img)
                key = cv2.waitKey(1)
                if key%256 == ord('q'):
                    return

if __name__ == '__main__':
    cv2.namedWindow(WINNAME)
    run()
```

## Camera capture
python opencv_sample_02.py

```python
from __future__ import print_function
import cv2

WINNAME = "OpenCV Sample 02"
WIDTH = 640
HEIGHT = 480

if __name__ == '__main__':
    cv2.namedWindow(WINNAME)
    cap = cv2.VideoCapture(0)
    if not cap.isOpened():
        sys.exit(1)

    while True:
        _, frame = cap.read()
        frame.resize((HEIGHT, WIDTH, 3))
        cv2.imshow(WINNAME, frame)
        key = cv2.waitKey(1)
        if key%256 == ord('q'):
            break
```

## Detect skin color region
