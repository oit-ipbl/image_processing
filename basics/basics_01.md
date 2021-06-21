# Tutorials for image processing

## Python3 basic tutorial
### Check version
You can check python and python module version with following command.
- python version
  ```sh
  % python --version
  ```
- python module version
  - show information of the all installed modules
    ```sh
    % python -m pip list
    ```
  - show information of the specified module
    ```sh
    % python -m pip show [module_name]
    % python -m pip show opencv-python
    ```
### Install python module
You can install modules with following command.
- install modules
  ```sh
  % python -m pip install [module_name]
  % python -m pip install opencv-python
  ```
- install modules at specified version
  ```sh
  % python -m pip install [module_name]==[version]
  % python -m pip install opencv-python=3.4.0
  ```
- upgrade module
  ```sh
  % python -m pip install -U [module_name]==[version]
  % python -m pip install -U opencv-python=3.4.0
  ```

## Python3 tutorial for image processing
### Basics of Python3 program
- Indentation is very important in Python programming. Indentation level is used to determine the coding block (the grouping of statements) and scope of variables.
- Variable is accessible from same block or nested block. Variable doesn't need to declare before using. Type of variable is determined by value to be assigned.Variable declared "global" has globally scope.
- A comment starts with a hash character (#)
- If you need more information about Python language, you can see the following site : [python3.8 docs](https://docs.python.org/3.8/index.html)
- Sample code
  ```python
  # example
  sum = 0
  for i in range(10):
    sum = sum + i
    print(str(i) + ":" + str(sum))  

  if sum <= 30 :
    print("sum is under 30")
  elif sum <= 50 :
    print("sum is between 30 and 50") 
  else:
    print("sum is over 50")
  ```
  <image src="../image/pys.png"><br>
  ```sh
  % python ./basic.py
  0:0
  1:1
  2:3
  3:6
  4:10
  5:15
  6:21
  7:28
  8:36
  9:45
  sum is between 30 and 50
  ```

### Cardinal modules in image processing
#### numpy (short name: np)
- more information: [numpy docs](https://numpy.org/doc/stable/)
- This module is the fundamental package for scientific computing.
  - a powerful N-dimensional array object
  - useful linear algebra, Fourier transform, and random number capabilities
- Sample code
  ```python
  import numpy as np
  a = np.zeros((4, 3, 2))  # make zero array whose size is (4,3,2)
  a[0:2, 1:2, 1] = 1  # Note that, 0:2 means 0 to 1, and 1:2 means 1.
  print(a)
  print(np.average(a))
  print(np.max(a))
  ```

#### cv2 (opencv-python)
- more information: [OpenCV4.5.2 docs](https://docs.opencv.org/4.5.2/)
- This is an open source module for Computer Vision.
- It has many functions for image processing.
- Sample code
  ```python
  import cv2
  img = cv2.imread('img/flash/ambient.jpg') # read image file
  bimg = cv2.GaussianBlur(img, (51,51), 5) # gaussian filter (size=(51,51),sigma=5)
  cv2.imshow('img',img)
  cv2.imshow('blur img',bimg)
  cv2.waitKey(0) # pause until press any key
  cv2.destroyAllWindows # close all cv2's windows
  ```

#### matplotlib.pyplot (short name: plt)
- more information: [matplotlib tutorials](https://matplotlib.org/tutorials/index.html), [pyplot](https://matplotlib.org/stable/api/pyplot_summary.html)
- This module is based on MATLAB.
- It has many functions for image processing.
- "plt" has many differences from "cv2". The following table shows the main examples in image IO.
  ||cv2|plt|
  |----|----|----|
  |color space (order of color channels)|BGR|RGB|
  |able to show multi-figures on a single window|x|o|
  |figure window|does not pause|pause and must be closed manually|
  |normalization at showing image|default:off|default:on|
  |displaying axis at showing image|default:off|default:on|
- Sample code
  ```python
  import matplotlib.pyplot as plt
  img = plt.imread('img/flash/ambient.jpg')
  plt.subplot(121),plt.imshow(img)
  plt.subplot(122),plt.imshow(255-img) # negative-positive conversion
  plt.show() # required for showing figure
  ```

### About digital image
#### Color (Additive color) 
- Many colors can be created by mixing the primary colors (Blue, Green, Red).<br>
  <image src="../image/Color-additive-mixing.png" height=25% width=25%><br>
  Additive color mixing([wikipedia](https://commons.wikimedia.org/wiki/File:Color-additive-mixing.png))

#### Digital images
- Digital images consist of many pixels. Pixel is the smallest unit in image space.
- Each pixel has a color (three values: Blue, Green, Red).<br>
  <image src="../image/pixels.png"><br>
  Digital image & pixels

#### Data structure of digital images
- Digital color images can be represented by 3 dimensional array.<br>
  <image src="../image/imageArray.png" height=30% width=30%><br>
  Color image array
- Range of pixel value is 0 to 255 (8bit). Thus, each pixel can create 16,777,216 (=(256)3) colors.    

### Image IO
まず，スクリプトと関数の違いについて紹介する．
#### Python Script
- Sample code
  ```python
  import cv2

  # read image file
  img = cv2.imread('img/flash/ambient.jpg')

  # some image processing ==============
  #img = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
  #   _____            _____
  #  /   B/__         /   R/__
  # /____/ G/__  ==> /____/ G/__
  #   /____/ R/        /____/ B/
  #     /____/           /____/
  # ====================================

  # write image file
  cv2.imwrite('res/res.png', img)

  # show image file
  cv2.imshow('window name', img)
  cv2.waitKey(0)  # pause until any key pressed
  cv2.destroyAllWindows()  # close all windows
  ```

#### Python Function
- We define a function of above script. It increases reusability.
- Sample code
  ```python
  import cv2

  def imageIOdemo():
      # read image file
      img = cv2.imread('img/flash/ambient.jpg')

      # some image processing ==============
      # img = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
      #   _____            _____
      #  /   B/__         /   R/__
      # /____/ G/__  ==> /____/ G/__
      #   /____/ R/        /____/ B/
      #     /____/           /____/
      # ====================================

      # write image file
      cv2.imwrite('res/res.png', img)

      # show image file
      cv2.imshow('window name', img)
      cv2.waitKey(0)  # pause until any key pressed
      cv2.destroyAllWindows()  # close all windows

  if __name__ == '__main__':
      imageIOdemo()
  ```

- cv2とpltの違いを体感する
- Sample code
  ```python
  import cv2
  import matplotlib.pyplot as plt

  def imageIOdemo():
      # read image file
      img_BGR = cv2.imread('img/flash/ambient.jpg')

      # some image processing ==============
      img_RGB = cv2.cvtColor(img_BGR, cv2.COLOR_BGR2RGB)
      #   _____            _____
      #  /   B/__         /   R/__
      # /____/ G/__  ==> /____/ G/__
      #   /____/ R/        /____/ B/
      #     /____/           /____/
      # ====================================

      # write image file
      cv2.imwrite('res/res1.png', img_BGR)
      cv2.imwrite('res/res2.png', img_RGB)

      # show images (cv2)
      cv2.imshow('img_BGR', img_BGR)
      cv2.imshow('img_RGB', img_RGB)
      # cv2.waitKey(0) # pause until any key pressed

      # show multi-images (plt)
      plt.subplot(1, 2, 1), plt.imshow(img_BGR), plt.title('img_BGR'), plt.axis('off')
      plt.subplot(1, 2, 2), plt.imshow(img_RGB), plt.title('img_RGB'), plt.axis('off')
      plt.show()
      # plt's figures must be closed manually.

      # close all cv2 windows (cv2)
      cv2.destroyAllWindows()


  if __name__ == '__main__':
      imageIOdemo()
  ```

#### How to resize input images
- The process with large size images is very heavy. If image size is huge, you should resize it to small.
  ```python
  # Resize the long side to the specified length
  def resizeImg(img, length):
      h, w, _ = img.shape

      if max(h, w) < length:
          return img

      if h < w:
          newSize = (int(h*length/w), length)
      else:
          newSize = (length, int(w*length/h))

      print('resize to', newSize)

      return cv2.resize(img, (newSize[1], newSize[0])) # (w, h)
  ```



    
  途中
