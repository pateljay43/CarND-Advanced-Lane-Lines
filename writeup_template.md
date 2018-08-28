## Writeup Template

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

[image1-1]: ./output_images/1_Original_Calibration.jpg "Original Distorted"
[image1-2]: ./output_images/2_Undistorted_Calibration.jpg "Undistorted"
[image2-1]: ./output_images/3_Original_Road.jpg "Original Road"
[image2-2]: ./output_images/4_Road_Transformed.jpg "Road Transformed"
[image3]: ./output_images/5_Threshold_Image.jpg "Binary Example"
[image4]: ./output_images/6_Unwraped_Image.jpg "Warp Example"
[image5]: ./output_images/7_Fit_Visual.jpg "Fit Visual"
[image6]: ./output_images/8_Output.jpg "Output"
[video1]: ./project_video_output.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the third code cell under functions `calibrart_camera()` and `test_calibrate_camera()` of the IPython notebook located at lines "./P2.ipynb".

I start to store important variables as globals in second code cell. And "object points" & "image points" are also part of that global variables. We are using a number of calibration input images taken from a camera at different angles which helps us to generate those global variables. First OpenCV function `findChessboardCorners` finds list of corners on a input image and they are passed into `calibrateCamera` function to generate distortion coefficient and camera calibration matrix. Also we save the object points and corners (image points) for use in further steps.

Here we can see an original distorted chessboard image which is calibrated and undistorted using OpenCV function `undistory` by passing in the camera calibration matrix and distortion coefficient.

![alt text][image1-1]
![alt text][image1-2]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2-2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used some combinations of color and gradient thresholds to generate a binary image (thresholding steps under `Color and Gradient transform` section in `P2.ipynb`).  I used gradient thresholds along X axis and directional threshold to mask area of interest. Also I used color thresholds on `Yellow` color to beter identify yellow lanes and S-channel of HLS color space which detects lanes very efficiently compared to other channels. Here's an example of my output for this step:

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `unwarp()`, which appears under section `Perspective transform` in the file `P2.ipynb`.  The `warper()` function takes as inputs an image (`img`) and calculates source (`src`) and destination (`dst`) points based on that image's shape. I chose the hardcode the source and destination points in the following manner:

```python
h,w = img.shape[:2]

# define source and destination points for transform
# values extracted by trial and test for straight line image
# such that the unwraped image will result into two parallel lanes
src = np.float32([(w/2-60,h/2+95),
                  (w/2+60,h/2+95), 
                  (w/2-380,h-50), 
                  (w/2+410,h-50)])

offset = 250
dst = np.float32([(offset,0),
                  (w-offset,0),
                  (offset,h),
                  (w-offset,h)])
```

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image for straight lines test image. But for our example image in this writeup we have chosen an image with curved lane to give a better idea of its working in hard screnario.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I used histogram and divided it in two parts for identifying ledt and right lane point distribution (max). Then using the sliding window search algorithm I created 10 windows count and used base points returned from histogram function as starting point. After identifying lane both lane lines I use 2nd degree polyfit using numpy's `polyfit`. Following image represents the output from this process:

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this under `Compute Radius of Curvature and Center offset` section of file `P2.ipynb` by following lecture #7 & #8 from Advanced Computer Vision Section.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step under section `Final Pipeline` in the file `P2.ipynb`. It has a `pipeline()` function that takes in original image `original_img` as argument and outputs a fully processed image throught every steps described above in this writeup. Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  
