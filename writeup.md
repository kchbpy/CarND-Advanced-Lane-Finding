
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

[image1]: output_images/cam.png "Distorted"
[image2]: output_images/test1_undist.jpg "Undistorted"
[image3]: output_images/test5_bin.png "Binary"
[image4]: output_images/warped.png "warp"
[image5]: output_images/fit1.png "fit"
[image6]: output_images/fit2.png "fit"
[image7]: output_images/result.png "output"
[image8]: output_images/2c.png ""
[image9]: output_images/3c.png ""
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located "./mypip.ipnb"

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image("calibration1.jpg") using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images("test1.jpg") like this one:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of S(channel of HLS),V(channel of HSV) and x gradient thresholds to generate a binary image. When I combinat these thresholds, I didn't use the "or" operation.I just add them togeter, then keep the points who's value is 2 or 3, drop the points who's value is 1.It's a bit like voting. In this way I can get more exact points of the lane line. (thresholding steps at lines 10 through 34 in `mypip.ipnb` second code cell).  Here's an example("test5.jpg") of my output for this step .

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform is contained in lines 36 through 57 in `mypip.ipnb` second code cell. I pick the source and destination points by hand, the points is:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 593, 450      | 290, 0        | 
| 685, 450      | 990 ,0        |
| 1117, 719     | 990, 719      |
| 194, 719      | 290, 719      |

I verified that my perspective transform was working as expected by testing the "straight_lines1.jpg" and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I defined a class called `Line` in the 4th code cell. It cotained some values and some methods. 
The method code "findlinepoints" at lines 69 through 152 in the 4th cell is used to find line in img and fit the line.

The reslut like this:

![alt text][image6] ![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I calculated the radius of curvature of one line in a method of the `Line` class named `calc_radius`.Then I calculate the average of left and right lines' radius in `drawlines` function.(line 33 in 6th code cell)

The class `Line` also have a method called `center_of_two_lines` to calculate the center of left and right lines, then I use the middle point of the frame to calculate the offset pixels and scale the pixels to meters.(line 48,49,50 in `drawlines` function)

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The function `drawlines` is defined for this step. Here is an example of my result on a test image:

![alt text][image7]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./video_out.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

1. The issues I meeted in this project

At fist I just use a combination of color and gradient thresholds like the method in this lesson to creat the binary image.
But when I tested the "test5.jpg" I got a bad binary image like this:
![image8]
So, I tried a lot of ways to get a good binary images.Finally I find a way(described above) to get the binary image like this:
![image9]

2. A potential way for the lane-line detection

I don't think the threshold is a good way to find lane pixels. The threshold way is worked for certen images. My pipline is good for this sunny day video. What if the wather changed like rainy day.I don't think my pipline can work well.

Maybe, we can use the deeplearning method to detect the lane-line. I'm not sure it would be a very good way, it's just a thought of my mind.