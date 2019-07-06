## Advanced Lane Finding
### Project 2 of the Udacity Self Driving Car Nanodegree

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

[image1]: .undistorted_readme.png "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: .threshold.png "Binary Example"
[image4]: .warped_img_lane_line2.png "Warp Example"
[image5]: .sliding_window.png "Sliding Window"
[image6]: .fit_poly.png "Fit Polynomial"
[image7]: ./hist_lane_line2.png "Histogram"
[image8]: ./processed_example.png "Example"
[video1]: ./project_video_output.mp4 "Video"


### Writeup / README

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the 5th code cell of the IPython notebook located in "./Lane\ Finding\ v2.ipynb"

I started by defining the 3D (z = 0, or fixed) object points used in real space, and the 2 dimensional image points used on the image plane. Next, I read in the images of the chessboard, and used openCV's `findChessboardCorners(gray, (nx,ny), None)` to append different points to the `objpoints` and `imgpoints` arrays.  Next, using these arrays and openCV's `calibrateCamera(objpoints, imgpoints, image_shape[:2],None,None)` functions, I was able to fidn the camera matrix and distortion coefficients needed to use openCV's `undistort(img, mtx, dist, None, mtx)` function, which would return our final undistorted image. Below is an example using the chessboard we read in earlier:


![alt text][image1]

### Pipeline (single images)

#### 1. Undistortion

Using the camera matrix and distortion correction coeffiecients found earlier, I simply used the `undistort()` method to apply the same function to one of our test images, which I've attached below:

![alt text][image2]

#### 2. Threshold

For my threshold functions (located in cells 6, 7 and 8 of "./Lane\ Finding\ v2.ipynb"), I used a combination of five threshold values. I started with a combined red and green with a threshold value of 150. Next I openCV's `cv2.cvtColor(img, cv2.COLOR_RGB2HLS)` to convert my image into hls format. From there I took out the s and the l layers for which I used a (100, 255) and (120, 255) threshold respectively. And finally I combined two threhsold layers, the first being the absolute value sobel threshold. Here I used a threshold of (10, 255) which worked well for the x direction. And finally I used the direction threshold to weed out any unnecessary lines that weren't between the radian values of (np.pi/6, np.pi/2). 

After weaving all of those together, I used a simple region mask to only take the region I was interested in. Below is an example from test image 1:

![alt text][image3]

#### 3. Perspective Transformation

The code for this cell 10 if my jupyter notebook file. Here I used a perspective transformer to transform the source points to the destination points using `M = cv2.getPerspectiveTransform(src, dst)`, and then `warped = cv2.warpPerspective(thresholded, M, img_size, flags=cv2.INTER_NEAREST)`. I chose the hardcode the source and destination points in the following manner:

```python
leftupperpoint  = [568,470]
rightupperpoint = [717,470]
leftlowerpoint  = [260,680]
rightlowerpoint = [1043,680]

src = np.float32([leftupperpoint, leftlowerpoint, rightupperpoint, rightlowerpoint])
dst = np.float32([[200,0], [200,680], [1000,0], [1000,680]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 568, 470      | 200, 0        | 
| 260, 680      | 200, 680      |
| 1043, 680     | 1000, 680     |
| 717, 470      | 1000, 0       |



![alt text][image4]

#### 4. Fitting a Polynomial

The code for this section begins in cell 11 and ends in cell 13 of my jupyter notebook file.

To fit a polynomial, I started by creating a histogram from the warped, thresholded image. This resulted in the graph below:
![alt text][image7]

From there I used the 'sliding window' method to start to create the second degree polynomial function.
![alt text][image5]

Finally I took the sliding window outputs of the left_x and right_x predictions with a margin of 50 to fit the polynomial to our test image, attached below:
![alt text][image6]

#### 5. Radius of Curvature

In cell 14 I found the radius of curvature of the image by first, finding the number of meters per pixel in both the x and y directions and then using the follow code `fit_cr = np.polyfit(y_points*ym_per_pix, x_values*xm_per_pix, 2)
                                           curverad = ((1 + (2*fit_cr[0]*y_eval*ym_per_pix + fit_cr[1])**2)**1.5) / np.absolute(2*fit_cr[0])` I was able to determine the radius of the curvature.

#### 6. Final Output (Image)

I then created the final pipeline combining all of the different functions and tested it on the image below:

![alt text][image8]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_output.mp4)

---


#### Problems with the pipeline

The biggest problem my pipeline faced was lighting conditions. You can see in the output of the [first challenge video](./challenge_video_output1.mp4), as the lighting conditions continue to change, my pipeline has a very difficult time finding the lane line again once it's lost it. This is most likely because of my threshold values, specifically the hls layers as the color shoulnd't change much. I will continue to test different parameters in the future to hopefully improve this pipeline.
