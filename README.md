# YOLOv5 on Jetson Nano Tips and Errors 🍕
This is to **share some tips and my personal experinece to solve errors** when installing YOLOv5 on Jetson Nano.

For anyone who would like to begin with "How to install YOLOv5 on Jetson Nano", please search it in google.

There are tons of githubs and blogs which kindly gives you the instructions on "how to install YOLOv5 on Jetson Nano" in google.

* [YOLOv5 developer's github](https://github.com/ultralytics/yolov5)

* [Website that you may refer to for "how to install YOLOv5 on Jetson Nano](https://sahilchachra.medium.com/setting-up-nvidias-jetson-nano-from-jetpack-to-yolov5-60a004bf48bc)

* * *

## Tip 1: Python version 😊
The requirement for Python version is >= 3.7.0, but YOLOv5 actaully works with the **pre-installed Python 3.6.9**.
* When you use pre-installed Python 3.6.9, you are recommended to use `pip3 install` command to install libraries.

You can see that YOLOv5 on Jetson Nano works with Python 3.6.9 in the images below!

<img src = "https://user-images.githubusercontent.com/78515689/159396237-0972a02b-5911-44b7-a864-e24bd61223b2.png" width="400px" height="270px"></img>
<img src = "https://user-images.githubusercontent.com/78515689/159396749-778182f0-e172-4ff4-b79a-b9d37b7ba4e0.jpg" width="400px" height="270px"></img>

## Tip 2: OpenCV compile 🤢
I tried to utilize the pre-installed opencv-python, but it kept giving me some errors.

For the ease of operation without trivial errors, I recommend you to install OpenCV in your Jetson Nano.

We all know that it is super annoying to install OpenCV in Ubuntu.

Surprisingly, [Q-engineering](https://qengineering.eu/install-opencv-4.5-on-jetson-nano.html) has kindly shared his technology to build OpenCV very easily.

Just click the link above, and follow his perfect instructions!

Things to be careful when installing OpenCV:
  * OpenCV version **4.5.2**: You need more memory for the version **4.5.2**. Hence, you need to edit two files. 
    * `/sbin/dphys-swapfile` → `CONF_MAXSWAP=4096` (this is only for OpenCV 4.5.2)
    * `/etc/dphys-swapfile` → `CONF_SWARSIZE=4096` (this is only for OpenCV 4.5.2)

  * OpenCV version other than 4.5.2: You only need to edit one file.
    * `/etc/dphys-swapfile` → `CONF_SWARSIZE=4096` (this is only for OpenCV version other than 4.5.2)

  * Once your build is successfully completed, you can see from `jtop` that OpenCV has been successfully compiled with CUDA.
  
<img src = "https://user-images.githubusercontent.com/78515689/159414692-acce5b4f-daaa-4e2c-b77f-84818c279463.JPG" width="400px" height="270px"></img>
  
## Tip 3: Change requirements.txt in yolov5 folder 😘
In my case, I uninstalled opencv-python. However, when running the yolov5 (detect.py), it kept installing the opencv-python and this eventually caused errors.

Therefore, I commented out opencv-python line in the **requirements.txt** file.

  * opencv-python>=version → # opencv-python>=version

## Tip 4: Change camera screen frame size for real-time detection 😜
Some of you guys may suffer from larger screen frame than the size of the monitor.

I suffered as I am using a mini monitor for Jetson Nano (check the image below).

<img src = "https://user-images.githubusercontent.com/78515689/159601538-4acda8fa-9f9f-4500-bc3c-d596789624b9.png" width="400px" height="270px"></img>

In this case, you can simply resolve this matter by checking the supported frame sizez of the camera, and add two more lines under the `class LoadStreams:` in **datasets.py** file.

For your information, the location of **datasets.py** is ***yolov5 → utils → datasets.py***

```
yolov5/
│   ...
└───utils/
    │   datasets.py
    │   ...
```

 0. ***I uploaded the edited datasets.py in this repository! Please read the followings before you use it!*** 🙃
 
 1. Check the supported frame sizes of the camera in the terminal.
 
    * `v4l2-ctl --list-formats-ext`
    * You can see that the supported frame sizes are various such as 640(width)x480(height), 800(w)x600(h) etc.
    
<img src = "https://user-images.githubusercontent.com/78515689/159601289-1e0a7950-b417-48dd-be2a-5da56f199a77.png" width="400px" height="270px"></img>
<img src = "https://user-images.githubusercontent.com/78515689/159601353-59f5585d-ea89-4f18-ac5a-98a4e8515fc1.png" width="400px" height="270px"></img>

 2. Add two more lines under the `class LoadStreams:` in **datasets.py** file.
 
    * ***Make sure to add only the supported frame sizes!***
    * `int(cap.set(cv2.CAP_PROP_FRAME_WIDTH, supported width value))`
    * `int(cap.set(cv2.CAP_PROP_FRAME_HEIGHT, supported height value))`
    
<img src = "https://user-images.githubusercontent.com/78515689/159602346-9a255892-85e6-4fae-be11-2b622e162d68.png" width="500px" height="270px"></img>

 3. If you add any unsupported values of width and height, you will receive GStreamer warning like the image below.

<img src = "https://user-images.githubusercontent.com/78515689/159603853-dd4a1f41-2d8b-418b-999d-44088cc79148.png" width="400px" height="270px"></img>

 4. Save the changes, and run YOLOv5 to check the frame size.

<img src = "https://user-images.githubusercontent.com/78515689/159603375-72be5013-15ce-4385-9f48-ec4cefe2796e.png" width="400px" height="270px"></img>
    
* * *

## Error 1: opencv illegal instructions (core dumped) 😑
  1. Open the **.bashrc** file in the terminal: `sudo vi ~/.bashrc` or `sudo nano ~/.bashrc`

  2. Add `export OPENBLAS_CORETYPE=ARMV8` at the bottom of the **.bashrc** file, and save/exit.

  3. Type `source ~/.bashrc` in the terminal to apply the setting without rebooting the system.

## Error 2: user warning: failed to load image python extension 😑
Try to downgrade your **torch** and **torchvision** versions.

Please check the compatible torch and torchvision versions in the [Nvidia developer forum](https://forums.developer.nvidia.com/t/pytorch-for-jetson-version-1-10-now-available/72048).

Go to the link attached above, click **Installation**, then you can see the compatible versions as below:

<img src = "https://user-images.githubusercontent.com/78515689/159426599-a274a345-bb20-4500-9ff9-0dcf9f78f8b4.png" width="400px" height="270px"></img>
<img src = "https://user-images.githubusercontent.com/78515689/159426709-fb42ac73-9700-4332-b21e-b40e59297da9.JPG" width="600px" height="270px"></img>

In my case, I installed torch 1.7.0 and torchvision 0.8.1, and did not get the error anymore.

<img src = "https://user-images.githubusercontent.com/78515689/159422796-def224dc-63f9-4c21-874e-75f99f9ca878.JPG" width="400px" height="270px"></img>

## Error 3: The_imagingft C module is not installed 😑
This is because your PIL has been compiled without **libfreetype**.

Try the following commands in the terminal.

`sudo apt-get install libfreetype6-dev`

`sudo pip3 uninstall pillow`

`sudo pip3 install Pillow==7.1.2`

 * I used `pip3` because I am using python3.

* * *
## co-researcher
Please visit my [**co-researcher's**](https://github.com/jinsoo9595) github as well!  
https://github.com/jinsoo9595
