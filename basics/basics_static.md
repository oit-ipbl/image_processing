# Image processing basics for static images

[README](../README.md)

---

## Objectives
- This page explains basics of digital images and image processing with Python3.

## prerequisite
- "[Image Processing Environment for iPBL](https://github.com/oit-ipbl/portal/blob/main/setup/python%2Bvscode.md)" has already been installed.
- The python programs (.py) have to be put under the directory `code`. And the all image files are saved/downloaded in the directory `imgs` and read from there.

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
### Directory structure for Image processing
- The python programs (.py) have to be put under the directory `code`. And the all image files are saved/downloaded in the directory `imgs` and read from there.
- Directory stucture
  ```
  +[code]                   <== work directory ("C:\oit\py22_ipbl\code")
  |
  |-+[img]                  <== this directory already exists.
  | |--Mandrill.png         <== this image already exists. ("C:\oit\py22_ipbl\code\imgs\Mandrill.png")
  | |--xxx.jpg              <== save new image files at this place.
  | |--xxx.png
  | |--
  |
  |--hands.py               <== this program already exists. ("C:/oit/py22_ipbl/code/hands.py")
  |--test.py                <== this program already exists.
  |--testWebcam.py          <== this program already exists.
  |--python programs(.py)   <== save new python programs at this place.
  |--
  ```

### Basics of Python3 program
- more information: [python3.9 docs](https://docs.python.org/3.9/index.html)
- Indentation is very important in Python programming. Indentation level is used to determine the coding block (the grouping of statements) and scope of variables.
- Variable is accessible from same block or nested block. Variable doesn't need to declare before using. Type of variable is determined by value to be assigned.Variable declared "global" has globally scope.
- A comment starts with a hash character `#`<br>
  <image src="../image/pys.png"><br>

#### Practice[basic]
- Save the following sample code as a python file and execute it. (`C:/oit/py22_ipbl/code/sample_basic.py`)
  - How to make new python files with VSCode
    - Confirm that the selected directory (or files directly under it) is the directory where you want to make a new python file.<br>
      <image src="../image/new_python_file_1.png" height=40% width=40%>
    - Click `New File` icon.<br>
      <image src="../image/new_python_file_2.png" height=40% width=40%>
    - Enter the name of the new file, including the extension (.py). Then the new python file is made.<br>
      <image src="../image/new_python_file_3.png" height=40% width=40%><image src="../image/new_python_file_4.png" height=50% width=50%>
  - The case that you make a new python file in another directory<br>
    <image src="../image/new_python_file_5.png" height=40% width=40%><image src="../image/new_python_file_6.png" height=35% width=35%>
- Sample code
  ```python
  # sample_basic.py
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
- It is O.K., if it is executed as follows.
  ```sh
  C:\\...\code> python sample_basic.py
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

### Important modules in image processing
#### numpy (short name: np)
- more information: [numpy docs](https://numpy.org/doc/stable/)
- This module is the fundamental package for scientific computing.
  - a powerful N-dimensional array object
  - useful linear algebra, Fourier transform, and random number capabilities

#### Practice[np]
- Save the following sample code as a python file and execute it. (`C:/oit/py22_ipbl/code/sample_numpy.py`)
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
  C:\\...\code> python sample_numpy.py
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
- more information: [OpenCV4.5.5 docs](https://docs.opencv.org/4.5.5/)
- This is an open source module for Computer Vision.
- It has many functions for image processing.

#### Practice[cv2]
- Save the following sample code as a python file, and execute it. (`C:/oit/py22_ipbl/code/sample_cv2.py`)
- Sample code
  ```python
  # sample_cv2.py
  import cv2
  img = cv2.imread('./img/Mandrill.png') # read image file
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
  <image src="../image/Mandrill_blur.png" height=50% width=50%>
- The windows close when you press any key.

#### :o:Checkpoint(basics and important modules)
- It's O.K., if you can finish the Practice[basic], [np] and [cv2].

### Script/Function in Python3 and image IO
- Making a Python script a function improves reusability.
  - Functions can be called by other python programs.

#### Python Script
  ```python
  # sample_imgIO.py
  import cv2

  # read image file
  img = cv2.imread('./img/Mandrill.png')

  # write image file
  cv2.imwrite('./img/res_scrpt.png', img)

  # show image file
  cv2.imshow('window name', img)
  cv2.waitKey(0)  # pause until any key pressed
  cv2.destroyAllWindows()  # close all windows
  ```

#### Python Function
  ```python
  # sample_imgIO_func.py
  import cv2

  def imageIO(img_name_in, img_name_out):
      # read image file
      img = cv2.imread(img_name_in)

      # write image file
      cv2.imwrite(img_name_out, img)
      return img

  def main():
      in_name = './img/Mandrill.png' # local
      out_name = './img/res_func1.png' # local
      img = imageIO(in_name, out_name)
      # show image file
      cv2.imshow('window name', img)
      cv2.waitKey(0)  # pause until any key pressed
      cv2.destroyAllWindows()  # close all windows

  # The following equation holds when this program file is only executed.
  if __name__ == '__main__':
      a = 1 # global
      main() # function name is free
  ```
#### Practice[script/function 1]
- Save the above two sample codes (`sample_imgIO.py`, `sample_imgIO_func.py`) as a python file. (`C:/oit/py22_ipbl/code/sample_imgIO.py`) (`C:/oit/py22_ipbl/code/sample_imgIO_func.py`)
- Execute the two python codes, respectively.
- It's O.K., if the two result images (`res_scrpt.png`, `res_func1.png`) in the directory `img` are the same.

#### Practice[script/function 2]
- Let's use the function `imageIO` in `sample_imgIO_func.py` on Other python programs!
- After `Practice[script/function 1]`, Save the following sample code as a python file, and execute it. (`C:/oit/py21/code/sample_other.py`)
- Sample code
  ```python
  # sample_other.py
  import sample_imgIO_func as myImgIO

  myImgIO.imageIO('./img/Mandrill.png', './img/res_func2.png')
  ```
- It's O.K., if the all result images (`res_scrpt.png`, `res_func1.png`, `res_func2.png`) in the directory `img` are the same.

#### :o:Checkpoint(script/function)
- It's O.K., if you can finish the Practice[script/function 1] and [script/function 2].

---

[README](../README.md)
