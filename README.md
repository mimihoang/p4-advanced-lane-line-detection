
## Advanced Lane Finding Project

The goals / steps of this project are the following:

- Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
- Apply a distortion correction to raw images.
- Use color transforms, gradients, etc., to create a thresholded binary image.
- Apply a perspective transform to rectify binary image (“birds-eye view”).
- Detect lane pixels and fit to find the lane boundary.
- Determine the curvature of the lane and vehicle position with respect to center.
- Warp the detected lane boundaries back onto the original image.
- Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./output_images/undistort_output.png "Undistorted"
[image2]: ./output_images/road_transform.png "Road Transformed"
[image3]: ./output_images/binary_combo.png "Binary Example"
[image4]: ./output_images/warped_straight_lines.png "Warp Example"
[image5]: ./output_images/example_output.png "Final Example"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  


## Camera Calibration

### Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code referenced in this README file is located at ./project4.ipython notebook. The logic for this section is in code cell #2 of the notebook.

There are two main steps to this process:

1. Read in chessboard images provided in the camera_cal folder 
2. Find the chessboard corners for each image(9x6 board)
3. Extract image points and object points from each chessboard image
4. Use OpenCV function cv2.calibrateCamera() and cv2.undistort() to compute the calibration and undistortion
5. Read in a chessboard image and pass it to the calibration and undistort functions to get the undistored image back. The result is plotted below. 

![][image1]


## Pipeline (test images)

### 1. Provide an example of a distortion-corrected image.

Using camera calibration and distortion coefficients returned from the calibration function, I pass them in to the undistort function to undo the distortion for one of the test image. You can find the code in code cell #3 and below is the result.

![][image2]

### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image. Provide an example of a binary image result.

I used a combination of color (channel S) and directional gradient thresholds to generate a binary image in code cell #4.  Here's an example of my output for this step.

![][image3]

### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

I did perspective transform in code cell #5. 
I followed the writeup example and used src and dst provided and confirmed that it worked well for my test. I verified it by drawing the src and destination points on the test and warped images. In the warped image the lines are parallel.

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

![][image4]


### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

The code for this section is in code cell #6 of the notebook. The methods are sliding_window and find_peaks. 


I now have a thresholded warped image to map out the lane lines. First I took a histogram along all the columns in the lower half of the image. Then I calculated the the peak of the left and right halves of the histogram. These will be the starting point for the left and right lines. After that I applied sliding window polynomials for each line.


### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The code for this finding radius of curvature and the position of the vehicle with respect to center is in code cell #7 of the notebook.

These are the steps to find the curvature of the lane line:

- Convert the image pixels space to meters
- Fit new polynomials to x,y in world space
- Calculate the new radii of curvature


To calculate the offset from centre:

- Find the midpoint in the image width
- Find the bottom pixel of the left and right lane lines
- Find the midpoint between the left and right lanes
- To calculate the offset, I took midpoint of the image widge - midpoint of the left and right lanes and convert to meter


### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The pipeline code to plot the final image back to the road with curvature and offset printed out can be found in code cell #8 and #9 of the notebook. This is what the final image looks like.

![][image5]

## Pipeline (video)

Provide a link to your final video output. Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!)

Here's a [link to my video result](./project_video_solution.mp4)

### Discussion
Briefly discuss any problems / issues you faced in your implementation of this project. Where will your pipeline likely fail? What could you do to make it more robust?

In building out the pipeline, I followed Udacity instruction courses and the goals/steps from the Writeup template. I built it one step at a time to make sure the result was what I expected before moving on to the next step. Ipython notebook is pretty useful in this because I could quickly visualize my work. I could have move the code out to python script files and run from the command line instead. 

These are the areas my pipeline can fail:
- Lanes with different colors
- Surface colors change dramatically, from asphalt to dirt road
- Sharp curves
- Driving at night

I would futher work on the color and gradient thresthold step to better detect the lane lines in varying road and lighting coditions. The solution that I currently have doesn't work too well on the challenge video so I'd like to work on that next.
