#Advanced Lane lines

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

[image1]: ./output_images/undistort_output.png "Undistorted"
[image2]: ./output_images/transformed.png "Road Transformed"
[image3]: ./output_images/binary.png "Binary"
[image4]: ./output_images/warped_straight_lines.png "Warped"
[image5]: ./output_images/lane_line_pixels.png "Lane line pixels"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Camera Calibration

####1. Camera matrix and distortion coefficients computation.

The code for this step is contained in the first section "Camera calibration & distortion correction." of the IPython notebook located in "./solution.ipynb".  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

###Pipeline (single images)

####1. Example of a distortion-corrected image.
Applying the undistortion transformation to a test image yields the following result (left distorted, right corrected):
![alt text][image2]

####2. Thresholded binary image using color and gradiend thresholds.

The code for this step is contained in the first section "Color transforms & gradients." of the IPython notebook located in "./solution.ipynb".

I used a combination of color and gradient thresholds to generate a binary image. For the color thresholding I worked in HLS space where I used the L and S channels. S channel is used for the saturation threshold and the L channel for a luminosity threshold filter. 
For the gradient thresholds I used Sobel function to check for horizontal and vertical gradients. Combination of those two methods yilded the best results. 
I did try applying magnitude thresholding using combined Sobel x and Sobel y together with thresholding an image for a given gradient range and Sobel kernel but this did not contribute to the better solution.

Here's an example of my output for this step.

![alt text][image3]

####3. Perspective transform.

The code for this step is contained in the first section "Color transforms & gradients." of the IPython notebook located in "./solution.ipynb".

The code for my perspective transform includes a function called `warp()` which takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points. I chose the hardcode the source and destination points in the following manner:

```
src = np.float32(
    [[190, 720], 
    [582, 457], 
    [701, 457], 
    [1145, 720]])
offset = [150,0]
dst = np.float32(
    [src[0] + offset, 
    np.array([src[0, 0], 0]) + offset, 
    np.array([src[3, 0], 0]) - offset, 
    src[3] - offset])
```
This resulted in the following source and destination points:

| Source         | Destination   | 
|:--------------:|:-------------:| 
| 190, 720       | 340, 720      | 
| 582, 457       | 340, 0        |
| 701, 457       | 995, 0        |
| 1145, 720      | 995, 720      |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

####4. Identiffying lane-line pixels and fittig their positions with a polynomial.

The code for this step is contained in the first section "Identiffying lane-line pixels and fittig their positions with a polynomial." of the IPython notebook located in "./solution.ipynb".

In order to decide which pixels of the binary image are part of the lines and which belong to the left and right line I have used "Peaks in histogram approach". In this approach the two most prominent peaks in the histogram are indicators of the x-position of the base of the lane lines. Once the peaks are identified I use sliding windows, placed around the lines centers, to identify where the line most likely goes from the bottom to the top of the image.

![alt text][image5]

When the lines have been found the first time, the full sliding windows calculation is no longer needed. Instead, using the primarly identified lines as a basis, I search within a margin of the previous line position.

MENTION LINE CLASS USAGE IN THE VIDEO PIPELINE

####5. Radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines # through # in my code in `my_other_file.py`

####6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `yet_another_file.py` in the function `map_lane()`.  Here is an example of my result on a test image:

![alt text][image6]

---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  


Add a counter to reset the search every X times (Note that the counter gets reset if the line is detected again before reaching five.)
Add checkes for lines parallel, curvature etc.
