# ios_logger
Application for logging camera images, accelerometer and gyroscope data,  gps and heading data, motion data and magnet data. 

This application was made for logging camera images and sensor data on Apple devices (iPad/iPhone) and is particularly geared towards performing intrinsic/extrinsic calibrations for VIO applications.

## Build and run:
1. Clone with ```git clone --recurse-submodules https://github.com/goromal/ios_logger.git```
2. Open ios_logger.xcodeproj in XCode
3. In project properties -> General set your team signing and make sure that signing certificate was successfully created
4. Download [OpenCV framework](https://sourceforge.net/projects/opencvlibrary/files/) and put it in project directory. 
  Or in project properties -> Build Settings -> Framework Search Paths add path to folder with OpenCV framework. 
  I suggest version 3.4.10.
5. Connect your device (you may have to wait for the debugger to be set up), select it (Product -> Destination) and run application (Product -> Run)

## Perform VIO Calibration for Your Device

Open the app on your device:

* set required image resolution in upper-left corner
* **if you check ARKit segment - app will use ARKit to get camera images (with ARKit native resolution - depends on device) + app will _log ARKit poses of the device_ (with origin in place where "START" button was pressed)**
* set switches with required sensors to be logged--_MUST_ at least include the IMU/Gyro sensors (toggled by default)
* _you can set AutoFocus on/off with "AF" button_
* _with off AutoFocus you can set camera focal lenth by slider in bottom-right corned_
* press "START" button
* when you want to stop collecting dataset press "STOP"

Connect your device to a Mac via USB:

* Open device filesystem, select _Files_
* Copy recorded dataset folder under the app section to your local machine (you can delete it off your device now to save space)

Now, on a machine that runs ROS:

* Clone this repository once again
* Transfer over the dataset folder
* ...

## Dataset format:
* Accel.txt: time(s(from 1970)),ax(g-units),ay(g-units),az(g-units)
* Gyro.txt: time(s),gx(rad/s),gy(rad/s),gz(rad/s)
* GPS.txt: time(s),latitude(deg),longitude(deg),horizontalAccuracy(m),altitude(m),verticalAccuracy(m),floorLevel,course(dgr),speed(m/s)
* Head.txt: time(s),trueHeading(dgr),magneticHeading(dgr),headingAccuracy(dgr)
* Motion.txt: time(s),attitude.quaternion.w,attitude.quaternion.x,attitude.quaternion.y,attitude.quaternion.z
* MotARH.txt: time(s),rotationRate.x(rad/s),rotationRate.y(rad/s),rotationRate.z(rad/s),gravity.x(g-units),gravity.y(g-units),gravity.z(g-units),userAccel.x(g-units),userAccel.y(g-units),userAccel.z(g-units),motionHeading(dgr)
* MotMagnFull.txt: time(s),calibratedMagnField.x(microteslas),calibratedMagnField.y(microteslas),calibratedMagnField.z(microteslas),magnFieldAccuracy
* Magnet.txt: time(s),magneticField.x(microteslas),magneticField.y(microteslas),magneticField.z(microteslas)
* ARposes.txt: time(s),ARKit.translation.x(m),ARKit.translation.y(m),ARKit.translation.z(m),ARKit.quaternion.w,ARKit.quaternion.x,ARKit.quaternion.y,ARKit.quaternion.z
* Frames.txt: time(s),frameNumber,_focalLenghtX,focalLenghtY,principalPointX,principalPointY_
* Frames.m4v: frames compressed in video 

## Other: ----
_To syncronize accelerometer and gyroscope data_ you can use python script sync-data.py:
```
python path_to_folder_with_sync-data/sync-data.py path_to_datasets_folder/dataset_folder
```

_To get frames from video you can use ffmpeg or some video editor._
For example: 
```
ffmpeg -i Frames.m4v Frames/Frame%05d.png -hide_banner
```
or you can try to use VideoToPictures.cpp:
```
compile VideoToPictures.cpp and
VideoToPictures path_to_datasets_folder/dataset_folder/Frames.m4v
```
