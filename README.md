# Installing-Tensorflow-with-GPU
Guide to installing Tensorflow with NVIDIA GPU. Use of GPU for compute only. Tested with GTX 1080 TI. Fixes login loop problem.
Installing Tensorflow for GTX 1080 ti
GPU For computation only.

Steps:
1. Download CUDA Toolkit 8.0 runfile(local) https://developer.nvidia.com/cuda-downloads

![CUDA Toolkit 8.0](https://github.com/nicolaifsf/Installing-Tensorflow-with-GPU/blob/master/Get%20CUDA%20Toolkit%208.0.png)


2. Download CUDNN from https://developer.nvidia.com/cudnn -> Might need to register for an account
- Agree to the Terms of the cuDNN Software License Agreement
- Download cuDNN v5.1 (Jan 20, 2017), for CUDA 8.0
- Select the cuDNN v5.1 Library for Linux and save the .tgz
![cudNN 5.1](https://github.com/nicolaifsf/Installing-Tensorflow-with-GPU/blob/master/Download%20cuDNN.png)

3. Navigate to www.nvidia.com/Download/index.aspx and download the runfile to install the 378 driver for GTX 1080 ti (or whichever driver suits you):
```
Product Type: GeForce
Product Series: GeForce 10 Series
Product GeForce GTX 1080 Ti
Operating System: Linux 64-bit
```
![NVIDIA 378 Driver](https://github.com/nicolaifsf/Installing-Tensorflow-with-GPU/blob/master/Download%20NVIDIA%20GTX%201080ti%20Driver.png)

4. Run ```sudo apt-get install build-essential```

5. ```$ sudo rm /etc/X11/xorg.conf ```
(It's ok if this doesn't exist)
6. Create the /etc/modprobe.d/blacklist-nouveau.conf file with :
```
$ sudo vim /etc/modprobe.d/blacklist-nouveau.conf
blacklist-nouveau.conf put:
blacklist nouveau
options nouveau modeset=0
```

Then ```$sudo update-initramfs -u```

7. Reboot your computer

8. On login screen, press ctrl + alt + f1 to open up login as tty1

9. Navigate to the folder where you downloaded the files from steps 1, 2 and 3

10. Run ```$ chmod +x cuda_8.0.61_375.26_linux.run```

11. Run ```$ chmod +x NVIDIA-Linux-x86_64-378.13.run```

12. Run ```sudo service lightdm stop```

13. Run ```$sudo apt-get remove --purge nvidia-*```
this is to ensure no previous NVIDIA drivers are installed.

14. Run ```$ sudo ./NVIDIA-Linux-x86_64-378.13.run --no-opengl-files```
The flag is important because installing opengl mucks up the installation
Just continue through the installation UNTILL IT ASKS YOU 'Would you like to run the nvidia-xconfig utility...' Hit NO for this

15. Run ```$sudo ./cuda_8.0.61_375.26_linux.run --no-opengl-libs```
```
Accept the EULA
No to Install NVIDIA Accelerated Graphics Driver for Linux-x86_64
Yes to Install the CUDA 8.0 Toolkit
Enter toolkit location to be  (default): /usr/local/cuda-8.0
Yes to install a symbolic link at /usr/local/cuda
Yes to install the CUDA 8.0 Samples
Default location for CUDA Samples location (or wherever you want them)
```

16. ```$ sudo modprobe nvidia```

17. Set Environment path variables (add to .bashrc):
```
export PATH=/usr/local/cuda-8.0/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:$LD_LIBRARY_PATH
```

18. Run ```$ source ~/.bashrc``` to load your new .bashrc

19. Run  ```$ cat /proc/driver/nvidia/version``` to verify installation

20. Check CUDA driver version:
```
$ nvcc -V 
```

21. Run ```$ sudo service lightdm``` start
There should be no issues at this point with logging in

22. Navigate to the CUDA samples we installed in step 15 and run ```$ make```
(Note, this step might take some time)

23. Navigate to: NVIDIA_CUDA-8.0_Samples/bin/x86_64/linux/release/ for the tests and run:
```
$ ./deviceQuery
```
to see your graphics card specs and
```
$ ./bandwidthTest
```
to check if its operating correctly.

Both should state they PASS

24. Reboot your computer 

25. Run:
```
$ tar -xzvf cudnn-8.0-linux-x64-v5.1.tgz
$ sudo cp cuda/lib64/* /usr/local/cuda/lib64/
$ sudo cp cuda/include/cudnn.h /usr/local/cuda/include/
```

26. ```$ sudo apt-get install libcupti-dev```

-> All CUDA dependencies for Tensorflow should now be installed

Installing Tensorflow
Downloading Tensorflow
27. ```$ git clone https://github.com/tensorflow/tensorflow```
(Install git with sudo apt install git if necessary)
28. ```$ cd tensorflow```

29. ```$ git checkout r1.0```

Installing Bazel (Used to build Tensorflow from Source)
30. ```$ sudo add-apt-repository ppa:webupd8team/java```

31. ```$ sudo apt-get update```

32. ```$ sudo apt-get install oracle-java8-installer```

33. ```$ echo "deb [arch=amd64] http://storage.googleapis.com/bazel-apt stable jdk1.8" | sudo tee /etc/apt/sources.list.d/bazel.list```

34. ```$ curl https://bazel.build/bazel-release.pub.gpg | sudo apt-key add -```
(might need to install curl here with sudo apt install curl)

35. ```$ sudo apt-get update && sudo apt-get install bazel```

Install dependencies
36.

a. Python 2.7 - ```$ sudo apt-get install python-numpy python-dev python-pip python-wheel```

b. Python 3.5 - ```$ sudo apt-get install python3-numpy python3-dev python3-pip python3-wheel```

37. Navigate to tensorflow directory $ cd tensorflow

38. Configure it ```$ ./configure```
Configure based on your preferences, specify the python version you want. e.g.: /usr/bin/python3
Be sure to:
```
say Y for installing with CUDA support
Specify CUDA version as 8.0
Specify cuDNN version as 5
Specify comma separated Cuda compute capabilities as 3.0
```

39. ```$ bazel build --config=opt --config=cuda //tensorflow/tools/pip_package:build_pip_package ```
(This might take a while)

40. Build .whl file ```$ bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg```

41. Install ```$ sudo pip3 install /tmp/tensorflow_pkg/tensorflow-1.0.1-cp35-cp35m-linux_x86_64.whl```

Note that the exact filename might be different, but it will still be in ```/tmp/tensorflow_pkg/``` if you followed the previous steps.

If you are using a different version of python, install with its corresponding pip instead.
42. Verify Installation by running python AFTER NAVIGATING OUT OF tensorflow directory
```
$ python3
```
```
>> import tensorflow as tf
>> hello = tf.constant('Hello, Tensorflow!')
>> sess = tf.Session(config=tf.ConfigProto(log_device_placement=True))
>> print(sess.run(hello))
```
```log_device_placement``` should show your GPU

CREDITS:

[This](https://devtalk.nvidia.com/default/topic/878117/-solved-titan-x-for-cuda-7-5-login-loop-error-ubuntu-1404) link was very helpful in installing the CUDA Toolkit (NeuroSurfer)

[This](https://devtalk.nvidia.com/default/topic/903867) was useful for learning how to remove existing NVIDIA drivers (gsuk)

