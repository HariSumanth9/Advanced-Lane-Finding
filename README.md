# Advanced-Lane-Finding
Project 2 of Self Driving Car Engineer Nano degree


## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./test6.png "Test Image"
[image2]: ./undistorted_test6.png "Undistorted Test Image"
[image3]: ./binary_threshold_test6.png "Color and Gradient Threshold"
[image4]: ./binary_warped_test6.png "Warped Lane Image"
[image5]: ./sliding_window_test6.png "Sliding Window"
[image6]: ./neighbour_search_test_6.png "Margin Search"
[image7]: ./Final_test6.png "Final Output"
[image8]: ./Chessboard.png "Undistorted Chessboard"
[video1]: ./project_video_output.mp4 "Project Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!
The code for this step is contained in IPython notebook located in "./P2.ipynb"

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the second code cell of the IPython notebook located in "./P2.ipynb"

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `object_points` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `image_points` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `object_points` and `image_points` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image8]

### Pipeline (single images)

I will illustrate the steps by using this test image.
![alt text][image1]

#### 1. Provide an example of a distortion-corrected image.

I use the distortion coefficients, camera matrix calculated above to undistort the images in this project. Applying `cv2.undistort()` on above test image gives the following undistorted image:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps in `col_grad_threshold()` in code cell 3). I used R, H, S, V channels and Sobel-x and Gradient direction. Here's an example of my output for this step.  (note: this is actually from one of the test images)

![alt text][image3]


#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warp_transform()`, which appears code cell 3 of P2.ipynb.  The `cv2.getPerspectiveTransform()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points and gives out M matrix.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32([[220, 720], [1100, 720], [570, 470], [722, 470]])
dst = np.float32([[320, 720], [920, 720], [320, 1], [920, 1]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 220, 720      | 320, 720      | 
| 1110, 720     | 920, 720      |
| 570, 470      | 320, 1        |
| 722, 470      | 920, 1        |

I verified that my perspective transform was working as expected by plotting the warped lane lines for the above binary thresholded image and identified that both left and right lane lines are parallel.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I applied sliding window and margin search method for finding lane pixels(code can be found in `sliding_window_lane_points()` and `neighbourhood_search_lane_points()` functions in code cell 3) I used `np.polyfit()` to fit a quadratic polynomial to those pixels.

![alt text][image5]
![alt text][image6]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines in code cell 3 in `radius_of_curvature()` and `center_distance()` functions.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in code cell 3 in `inverse_transform()` where I unwarp and plot back the lane lines on the road images.  Here is an example of my result on a test image:

![alt text][image7]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_output-2.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I encounter wobbly lines in project video due to problems like lighting conditions, shadows, discoloration. My pipeline wobbled a bit in the last few frames of the video. I would suggest a deep learning based semantic segmantation techique for robust lane identification.

