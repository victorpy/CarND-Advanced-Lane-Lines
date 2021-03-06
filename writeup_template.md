##Writeup Template
###You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

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

[image0]: ./images/undistort_output.png "Undistorted chess"
[image1]: ./images/undistorted_example.jpg "Undistorted"
[image2]: ./images/combined_binary_example.jpg "Combined Binary"
[image3]: ./images/sliding_windows.png "Binary Example"
[image4]: ./images/warped_example.jpg "Warp Example"
[image5]: ./images/final_result.jpg "Final Result"
[video1]: ./output.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

###Camera Calibration

####1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

####1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell named " compute the camera calibration using chessboard images" of the IPython notebook located in "./examples/example.ipynb".  

I start by preparing "object points", which will be the coordinates of the chessboard corners in the real world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to a test image of a distorted chessboard using the `cv2.undistort()` function and obtained this result: 

![alt text][image0]


###Pipeline (single images)

####1. Provide an example of a distortion-corrected image.
I apply the distortion correction to an image, to test the correction obtained in the previous camera calibration. The resulting images is this:
![alt text][image1]
####2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding function is in the section "Create thresholded binary image" in the notebook located in "./examples/example.ipynb" ). I converted to HLS and and separated the S channel. With that and gradient threshold i obtain the best results. Here's an example of my output for this step.  

![alt text][image2]

####3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warp()`, which you can see in the section Perspective Transform with thresholded image of the IPython notebook.  The `warp()` function takes as inputs an image (`img`).  I chose the hardcode the source and destination points inside the function in the following manner:

```
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

![alt text][image4]

####4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

You can see in section Sliding Windows Search of the "./examples/example.ipynb"  the functions `sliding_windows_search()` that perform the look up of the pixels based based on a histogram of the image, `extract_pixel_positions()` that get the pixels positions of the lines and `get_poly_fit()` to get the fit my lane lines with a 2nd order polynomial like this:

![alt text][image3]

####5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I extracted a new polynomial fit for real world coordinates in meters and calculated the radio with this new values.  You can see in section Measuring Curvature in "./examples/example.ipynb".

####6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `yet_another_file.py` in the function `map_lane()`.  Here is an example of my result on a test image:

![alt text][image5]

---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./output.mp4)

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The most difficult problems to solve where the different shadows on the road, the different colors of the road, and the cars that pass very close and could count as a line in the histogram, this could distort the line identification.

i could improve the line identification in the histogram playing with the parameters of sliding windows search. Also implement a better approach using more the mean values of fit polynoms and x,y pixels values of the line in the processing of the image processing. This could avoid sudden changes in the lines detection, when there are external factors affecting the correct identification of the lines. Like i mention before, cars that pass to close to the lines, different colors of the road, and shadows.
 


