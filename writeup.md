

**Advanced Lane Finding**

Mine steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Check if the detected lane is reasonbale.
* Track detected lanes of several frames and do weighted average to smooth the lane.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: https://raw.githubusercontent.com/Awesomex005/CarND-Advanced-Lane-Lines/master/output_images/calibration2_dist_vs_undist.jpg
[image2]: https://raw.githubusercontent.com/Awesomex005/CarND-Advanced-Lane-Lines/master/output_images/undist_raw_img.jpg
[image3]: https://raw.githubusercontent.com/Awesomex005/CarND-Advanced-Lane-Lines/master/output_images/threshold_bin_image.jpg
[image4]: https://raw.githubusercontent.com/Awesomex005/CarND-Advanced-Lane-Lines/master/output_images/straight_lines_undist_warped.jpg
[image5]: https://raw.githubusercontent.com/Awesomex005/CarND-Advanced-Lane-Lines/master/output_images/find_lane_base.jpg
[image6]: https://raw.githubusercontent.com/Awesomex005/CarND-Advanced-Lane-Lines/master/output_images/fit_line.jpg
[image7]: https://raw.githubusercontent.com/Awesomex005/CarND-Advanced-Lane-Lines/master/output_images/project_video_Moment.jpg
[video1]: https://raw.githubusercontent.com/Awesomex005/CarND-Advanced-Lane-Lines/master/output_videos/project_video.mp4 "Video"


### Camera Calibration

#### 1. Camera matrix and distortion coefficients

The code for this step is contained in the first & second code cell of the IPython notebook located in `advance_Lane_finding.ipynb`.

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result:

![alt text][image1]

### Pipeline (single images)

#### 1. a distortion-corrected image

To demonstrate this step, I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. thresholded binary image

I used a combination of saturation, lightness and gradient thresholds to generate a binary image (thresholding steps at bottom of the `4th` code cell of `advance_Lane_finding.ipynb`).  Here's an example of my output for this step.

![alt text][image3]

#### 3. Perspective transform

The code for my perspective transform appears in the 6th code shell of `advance_Lane_finding.ipynb`.  I used `cv2.getPerspectiveTransform` to get transform matrix, this function requires as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
PT_SRC = np.float32([[594,450],
                    [688,450],
                    [1120, 720],
                    [200, 720]])
PT_DST = np.float32([[320, 2],
                    [img_sz[0] - 320, 2],
                    [img_sz[0] - 320, img_sz[1] - 2],
                    [320, img_sz[1] - 2]])
```

This resulted in the following source and destination points:

| Source        | Destination   |
|:-------------:|:-------------:|
| 594, 450      | 320, 2        |
| 688,450      | 960, 2      |
| 1120, 720    | 960, 718      |
| 200, 720      | 320, 718        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Identify lane-line pixels and fit their positions with a polynomial

The code of this step appears in the `7th & 8th` code shell of `advance_Lane_finding.ipynb`.
The steps I used to identify lane-line pixels and fit them with a polynomial are following:
* The first step is to get the histogram of half bottom of the thres_warped_img and split the histogram into two sides and determine each lane line's base.
![alt text][image5]
* Then use sliding window search to determine each lane-line's pixels
* Finally fit polynomial for each lane-lines
![alt text][image6]

#### 5. Calculated the radius of curvature of the lane and the position of the vehicle with respect to center

The code of this step appears in the `11th` code cell of `advance_Lane_finding.ipynb`.

#### 6. Project the result back down onto the road

The code of this step appears in the `12th` code cell of `advance_Lane_finding.ipynb`.  Here is an example of my result on a test image:

![alt text][image7]

---

### Pipeline (video)

#### 1. The result video

The code of the entire pipeline appears in the `13th` code cell of `advance_Lane_finding.ipynb`.

Here's a [link to my video result](https://raw.githubusercontent.com/Awesomex005/CarND-Advanced-Lane-Lines/master/output_videos/project_video.mp4)

---

### Discussion

**problems**
Our perspective transform `src` & `dst` are hard coded, if the road is bumpy, the camera along with the camera goes up and down, the pipeline could failed.
**Probable Solution**
Maybe we could levelage Inertial Navigation Sensors Data to dynamically determine our perspective transform parameters.
