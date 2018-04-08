## Writeup Report for P4

---

**Advanced Lane Finding Project**

The steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Try color space transforms and gradients, through experiments finally adopt L-channel and B-channel to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./test_images/test1.jpg "Color Space Transformed"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Curvature and Vehicle Position"
[image7]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in cells from 1 to 4 of the IPython notebook located in "./advanced-lane-line-detection.ipynb".   

I start by preparing "object points", which will be the (x, y, z) 3D coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) 2D plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I use camera calibration coefficients, i.e., mtx and dist, to undistort for each image using the `cv2.undistort()` function. Some test images like this one:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
I have tried different color space and gradients transformation. First, I tried the combination of S-channel, gradient x and direction like learned in class. However, when there is shadow, the lane lines are not detected well. Through the discussions in slack forum, I tried use the combination of the L-channel in HLS color space and B-channel in LAB color space. Since the thresholded L-channel is good at detecting white lanes and B-channel
is good at yellow lanes, the performance is more resistant to shadow. 
The steps and codes are illustrated in cells from 17 to 30 in "./advanced-lane-line-detection.ipynb". Here are some examples of my output for this step. 

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warp_imgs()`, which appears in cell 31 in the IPython notebook.  The `warp_imgs()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32(
    [[585, 450],
    [190, 720],
    [700, 450],
    [1120, 720]])
dst = np.float32(
    [[200, 0],
    [200, 720],
    [1000, 720],
    [1000, 0]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 585, 450      | 200, 0        | 
| 190, 720      | 200, 720      |
| 700, 450      | 1000, 720     |
| 1120, 720     | 1000, 0       |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I used some codes in class to detect the lanes in warped binary images. The sliding box and histogram method were used.  The original lane lines, sliding box, and a highlighted 2nd order polynomial are like this:

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in cells from 67, 68 in Ipython notebook. The curvature calculation taught in class was used in this part. Some results for test images are shown:

![alt text][image6]

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in cells 70-73 in the Ipython notebook with function `draw_lane()` and `draw_data()`.  Here is an example of my result on a test image:

![alt text][image7]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Since gradient method to detect lanes does not perform well, I only used color space transformation in pipeline. To effectly detect yellow and white lane lines, I combined L-channel and B-channel of the image. 
