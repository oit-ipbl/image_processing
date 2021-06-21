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
- If you need more information about Python language, you can see the following site : https://docs.python.org/3.8/index.html;
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

