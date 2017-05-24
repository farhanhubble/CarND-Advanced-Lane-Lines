# Advanced Lane Finding
![Identifying Lanes](readme-resources/output.gif)

## [Udacity Self Driving Car Nanodegree: Computer Visison Project](https://in.udacity.com/course/self-driving-car-engineer-nanodegree--nd013/)


The goal of this project was to identify driving lanes in videos using computer vision techniques. Here is a step-by-step summary of the how the code works:

1. Camera calibration matrix and distortion coefficients are obtained from included calibration chessboard images.
2. Raw image is undistorted using the matrix and coeefiicients obtained in the calibration step.
3. A combination of Sobel transforms and thresholding in HSV colorspace is used to higlight lane edges in the image.
4. The perspective of the transformed image is changed to top view or bird's eye view.
5. Pixels beonging to lanes are identified using sliding window search.
6. Curvature of the road is estimated from the identified lane pixels.
7. A polynomial is fitted to the lane pixels.
8. Lanes are plotted on top of the top view of the image using the polynomial fitted above.
9. An inverse perspective transform is performed, so the plotted lanes and curvature information transfer back to the original image.
10. In case of a video all the steps above are repeated or every frame.

Details of these steps follow:

## Camera Calibration
The purpose of camera calibration is to estimate the parameters of the lenses and image sensor of a camera. This information is then used to remove distortions caused by that camera. To calibrate the camera two lists of coordinates are needed: The first list, called object points, contains the coordinates of some arbitrary points on a real-world objects while the second list, called image points, has the coordinates of the same points in camera images of the object. For this project we used the 20 chessboard calibration images provided by Udacity in the directory [camera_cal](https://github.com/farhanhubble/CarND-Advanced-Lane-Lines/tree/master/camera_cal). Object points were generated in code assuming 9 internal corners in the horizontal direction and 6 in the vertical direction in every image and corresponding image points were obtained by calling **cv2.findChessboardCorners()**. This API was able to identify imgae points in 17 out of 20 images.

The object points, image points were then passed to **cv2.calibrateCamera()**, which is the API that calculates the camera parameters. Shown below is one of the calibration images with chessboard corners drawn over it.

![chess with corner markers](readme-resources/corners.png)

Once the calibration was done, the following steps were executed for every video frame.

## Undistortion
Every video frame was undistorted using the parameters obtained in the calibration step. **OpenCV** API **cv2.undistort()** was used for this purpose. The section named **Testing Undistortion** has some test code for monitoring the effect of undistortion. Undistortion was first tested on the calibration images themselves. Here is one such image and its undistorted version compared side by side.
![Distorted image, undistorted image](readme-resources/undistort.png)

Here is how an image taken from the car's camera looks like before and after undistortion.
![Distorted image, undistorted image](readme-resources/undistorted2.png)

Effects of undistortion are most noticeable near the edges of the images. In the image of the road, the car's hood, visible at the bottom has shrunk substantially.

## Binary Mask Evaluation
To find edges in the frame image a set of Sobel transfroms were combined with thresholding in the HLS colorspace. The thresholds for both Sobel transforms as well as color threshoding were computed manually by trial and error. This was done only once and not performed for every frame. 

Some of the masks that were tested can be visualized by enabling the **__DEBUG__** flag in the topmost cell and executing the function **visualize_candidate_masks()**. Here's a sample image from the experimentation phase.

![Visualizing edge masks](readme-resources/visualize-all.png)

Final value of the thresholds and the right combination masks were decided based on the following observations:
1. X and Y Sobel masks alway identify edges belonging to lane markings but also include other edges.
2. A Sobel angle mask will have dense regions where yellow markings are present but has uniform noise.
3. S channel highlights the lane markings clearly if images have good saturation.
4. L channel can be combined with S channel to exclude edges from low-lit areas.

Final thresholds are avilable throughout the notebook by calling **get_thresholds()** and the combined mask used on all video frames is obtained by calling **get_lane_mask()**.  






