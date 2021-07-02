# Basics of image processing

## Objectives
- This page explains basics of digital images and image processing with Python3.

## prerequisite
- "[Image Processing Environment for iPBL](https://github.com/oit-ipbl/portal/blob/2e4097811b9ddfcdf6e5ac80b4ffa5f66edb6c32/setup/python+vscode.md)" has already be installed.

## Basics of digital images
### Color (Additive color) 
- Many colors can be created by mixing the primary colors (Blue, Green, Red).<br>
  <image src="../image/Color-additive-mixing.png" height=25% width=25%><br>
  Additive color mixing([wikipedia](https://commons.wikimedia.org/wiki/File:Color-additive-mixing.png))

### Digital images
- Digital images consist of many pixels. Pixel is the smallest unit in image space.
- Each pixel has a color (three values: Blue, Green, Red).<br>
  <image src="../image/pixels.png"><br>
  Digital image & pixels

### Data structure of digital images
- Digital color images can be represented by 3 dimensional array.<br>
  <image src="../image/imageArray.png" height=30% width=30%><br>
  Color image array
- Range of pixel value is 0 to 255 (8bit). Thus, each pixel can create 16,777,216 (=(256)3) colors.    



## Image processing with Python3
### Basics of Python3 program
- Indentation is very important in Python programming. Indentation level is used to determine the coding block (the grouping of statements) and scope of variables.
- Variable is accessible from same block or nested block. Variable doesn't need to declare before using. Type of variable is determined by value to be assigned.Variable declared "global" has globally scope.
- A comment starts with a hash character (#)
- If you need more information about Python language, you can see the following site : [python3.8 docs](https://docs.python.org/3.8/index.html)
- Sample code
  ```python
  # basic.py
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
  C:\oit\py21\code> python basic.py
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

#### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint("numpy")
- Save the following sample code as a python file and execute it.
- Sample code
  ```python
  # sample_numpy.py
  import numpy as np
  a = np.zeros((4, 3, 2))  # make zero array whose size is (4,3,2)
  a[0:2, 1:2, 1] = 1  # Note that, 0:2 means 0 to 1, and 1:2 means 1.
  print(a)
  print(np.average(a))
  print(np.max(a))
  ```
- It is O.K., if it is executed as follows.
  ```sh
  C:\oit\py21\code> python sample_numpy.py
  [[[0. 0.] 
    [0. 1.] 
    [0. 0.]]

   [[0. 0.] 
    [0. 1.] 
    [0. 0.]]

   [[0. 0.] 
    [0. 0.] 
    [0. 0.]]

   [[0. 0.]
    [0. 0.]
    [0. 0.]]]
  0.08333333333333333
  1.0
  ```

#### cv2 (opencv-python)
- more information: [OpenCV4.5.2 docs](https://docs.opencv.org/4.5.2/)
- This is an open source module for Computer Vision.
- It has many functions for image processing.

#### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint("cv2")
- Save the following sample code as a python file.
- Save the image "[lena.jpg](../image/lena.jpg)" in the same directory.
- Execute the python code.
- Sample code
  ```python
  # sample_cv2.py
  import cv2
  img = cv2.imread('lena.jpg') # read image file
  if img is None: # maybe Path is wrong
      print("image file is not opened.")
      exit(1)
  bimg = cv2.GaussianBlur(img, (51,51), 5) # gaussian filter (size=(51,51),sigma=5)
  cv2.imshow('img',img)
  cv2.imshow('blur img',bimg)
  cv2.waitKey(0) # pause until press any key
  cv2.destroyAllWindows # close all cv2's windows
  ```
- It is O.K., if the following windows pop up.<br>
  <image src="../image/lena_blur.png" height=50% width=50%>
- The windows close when you press any key.

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

#### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint("plt")
- Save the following sample code as a python file.
- Save the image "[lena.jpg](../image/lena.jpg)" in the same directory.
- Execute the python code.
- Sample code
  ```python
  # sample_plt.py
  import matplotlib.pyplot as plt
  img = plt.imread('lena.jpg')
  if img is None: # maybe Path is wrong
      print("image file is not opened.")
      exit(1)
  plt.subplot(121),plt.imshow(img)
  plt.subplot(122),plt.imshow(255-img) # negative-positive conversion
  plt.show() # required for showing figure
  ```
- It is O.K., if the following window pops up.<br>
  <image src="../image/lena_plt.png" height=50% width=50%>
- The window closes when you click [x] button at upper right of the window.


### Script/Function in Python3 and image IO
- All of the Python programs introduced above is a sequential program called scripts.
- Making a Python script a function improves reusability.
  - Functions can be call by other python programs.
#### Python Script
- Sample code
  ```python
  # sample_imgIO.py
  import cv2

  # read image file
  img = cv2.imread('lena.jpg')

  # some image processing ==============
  img = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
  #   _____            _____
  #  /   B/__         /   R/__
  # /____/ G/__  ==> /____/ G/__
  #   /____/ R/        /____/ B/
  #     /____/           /____/
  # ====================================

  # write image file
  cv2.imwrite('res.png', img)

  # show image file
  cv2.imshow('window name', img)
  cv2.waitKey(0)  # pause until any key pressed
  cv2.destroyAllWindows()  # close all windows
  ```

#### Python Function
- Sample code 1
  ```python
  # sample_imgIO_func.py
  import cv2

  def imageIO(img_name_in, img_name_out):
      # read image file
      img = cv2.imread(img_name_in)

      # some image processing ==============
      img = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
      #   _____            _____
      #  /   B/__         /   R/__
      # /____/ G/__  ==> /____/ G/__
      #   /____/ R/        /____/ B/
      #     /____/           /____/
      # ====================================

      # write image file
      cv2.imwrite(img_name_out, img)
      return img

  def main():
      in_name = 'lena.jpg' # local
      out_name = 'res_func1.png' # local
      img = imageIOdemo(in_name, out_name)
      # show image file
      cv2.imshow('window name', img)
      cv2.waitKey(0)  # pause until any key pressed
      cv2.destroyAllWindows()  # close all windows

  # The following equation holds when this program file is only executed.
  if __name__ == '__main__':
      a = 1 # global
      main() # function name is free
  ```
- Sample code 2
  ```python
  # other.py
  import sample_imgIO_func as myImgIO

  myImgIO.imageIO('lena.jpg', 'res_func2.png')
  ```

#### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint("python function")
- 上記を試させる

#### Difference of cv2 and plt
- Sample code
  ```python
import cv2
import matplotlib.pyplot as plt

def imageIOdemo(img_name_in, img_name_out_bgr, img_name_out_rgb):
    # read image file
    img_BGR = cv2.imread(img_name_in)

    # some image processing ==============
    img_RGB = cv2.cvtColor(img_BGR, cv2.COLOR_BGR2RGB)
    #   _____            _____
    #  /   B/__         /   R/__
    # /____/ G/__  ==> /____/ G/__
    #   /____/ R/        /____/ B/
    #     /____/           /____/
    # ====================================

    # write image file
    cv2.imwrite(img_name_out_bgr, img_BGR)
    cv2.imwrite(img_name_out_rgb, img_RGB)

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
    imageIOdemo('lena.jpg', 'res_bgr.png', 'res_rgb.png')
```

#### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint("cv2 vs plt")
- 上記を試させる

    
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

#### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)Checkpoint("resize")
- 上記を試させる

