## Writeup Alisson Freitas

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

[image1]: ./output_images/Calibrated_Camera_Output.jpg "Undistorted Output"
[image2]: ./output_images/undistorted_image.jpg "Road Transformed"
[image3]: ./output_images/binary_combo_output.jpg "Binary Output"
[image4]: ./output_images/warped_straight_lines.jpg "Warp Example"
[image5]: ./output_images/color_fit_lines.jpg "Fit Visual"
[image6]: ./output_images/example_output.jpg "Output"
[image7]: ./output_images/curve_radius.jpg "Curve Radius"
[video1]: ./test_videos_output/project_video.mp4 "Video"


### Writeup / README

The code for this project is contained file in the Jupyter Notebook located in the root of this project "./P2.ipynb".  

### Camera Calibration

#### 1. The fist step is compute the camera matrix and distortion coefficients to undistort the image.

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate a distortion-corrected image you can see it below:
![alt text][image2]

#### 2. In the next steps I used color transforms, gradients and the HLS color space to create a thresholded binary image.

I used a combination of color and gradient thresholds to generate a binary image (8th step in `P2.ipynb`).  Here's an example of my output for this step.

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warp()`, which appears in the file `P2.ipynb`.  The `warp()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32(
    [[(img_size[0] / 2) - 55, img_size[1] / 2 + 100],
    [((img_size[0] / 6) - 10), img_size[1]],
    [(img_size[0] * 5 / 6) + 60, img_size[1]],
    [(img_size[0] / 2 + 55), img_size[1] / 2 + 100]])
dst = np.float32(
    [[(img_size[0] / 4), 0],
    [(img_size[0] / 4), img_size[1]],
    [(img_size[0] * 3 / 4), img_size[1]],
    [(img_size[0] * 3 / 4), 0]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 585, 460      | 320, 0        | 
| 203, 720      | 320, 720      |
| 1127, 720     | 960, 720      |
| 695, 460      | 960, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

Also I defined a function called `unwarp()` to revert this process.

![alt text][image4]

#### 4. I identified lane-line pixels and fit their positions with a polynomial

Then I located the lane lines summing across image pixels vertically, the highest areas of vertical lines will appear as larger values.

After that I implemented a series of sliding windows that are positioned in the mean position of the higher nonzero pixel count. 
    

![alt text][image5]

#### 5. I calculated the radius of curvature of the lane.

First I defined conversions in x and y from pixels space to meters then I execute the calculation of curve radius (in meters)

![alt text][image7]

#### 6. Result plotted back down onto the road such that the lane area is identified clearly.


![alt text][image6]

---

### Pipeline (video)

#### 1. Then finally I consolidated apply all previous process in a function called `pipelinefinal` to apply to the video.

Here's a [link to my video result][video1]

### 2. Potential shortcomings with your current pipeline

One potential shortcoming would be what would happen when we have lane changes or some unknown object blockin the lane identification. The algorithm might not work propperly in may be inpredictable in cases that may occur in not ideal conditions;

Another shortcoming could be that the video annotation is very unstable and this might lead to errors in a real world scenario.


### 3. Suggest possible improvements to your pipeline

Tweak the gradient thresholds to improve the detection of lane lines edges in different lighting conditions would be ideal to cover most scenarios;

A possible improvement would be to use machine learning and AI to improve the reliability and the model prediction.