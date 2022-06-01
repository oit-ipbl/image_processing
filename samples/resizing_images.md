# Resizing images
- The process with large size images is very heavy. If image size is huge, you should resize it to small.
- There are various methods for resizing.
  - Resize with specified size
    ```python
    # the size of img_resize becomes (new_width, new_height).
    img_resize = cv2.resize(img, (new_width, new_height))
    ```
  - Resize with scalling
    ```python
    # downscalling -> 1/2
    img_resize = cv2.resize(img, None, fx=1/2, fy=1/2)
    ```
  - Resize the long side to the specified length
    - This program can resize Images of various sizes to approximately the same data size while maintaining the aspect ratio.
    ```python
    # ###########################################
    # res = resizeImg(src, length)
    # src: input image
    # length[int]: length of long side after resizing
    # res: resized image
    # ###########################################
    def resizeImg(img, length):
        h, w = img.shape[:2]

        if max(h, w) < length: # do not need resizing
            return img

        if h < w:
            newSize = (int(h*length/w), length)
        else:
            newSize = (length, int(w*length/h))

        print('resize to', newSize)

        return cv2.resize(img, (newSize[1], newSize[0])) # (w, h)
    ```

## Exercise[resizing]
- modify the function `imageIO` in `sample_imgIO_func.py` of`Practice[script/function 1]` to be able to save the half size (256x256) image by using above three resizing methods, respectively.
- It's O.K., if size of the result image `res_func1.png` is 256x256 as follows.<br>
  <image src="../image/lena_512.png" height=40% width=40%><image src="../image/lena_256.png" height=39% width=39%>

## :o:Checkpoint(resizing)
- It's O.K., if you can finish the Exercise[resizing].
