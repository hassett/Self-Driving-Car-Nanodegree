# **Finding Lane Lines on the Road** 

## Project 1: Udacity Self-Driving Car Nanodegree

### An attempt to design a pipleine capable of detecting lane lines on basic, straight roads

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. The Pipeline

My pipeline consisted of 6 steps.

1) I started by converting the image to grayscale, in order to simplify the image.

2) I then applied a Gaussian Blur to the image, as to try and get rid of sharp edges in the image

3) I then applied Canny Edge detection using a low_thresh value of 50, and a high_thresh value of 250.

4) Then, I simplified the image to our region of interest, because in our case we can assume the camera will be fixed

5) Next, I applied the Hough Transformation which connects all the dots created by the canny edge detection

6) And finally, I weighed the images to create an overlay onto the original image.


### 2. Potential Shortcomings in the Pipeline


One of the most significant shortcomings of this pipeline, would be the innability to determine a lane line if there were say a
shadow ontop of the lane line. Since we are only checking the gradient from one pixel to another, if they were similar enough, 
we may not be able to determine if it was a lane line or not.

Another shortcoming, is the fact that the pipeline will only be able to output straight lines, so if the car were say going around a curve, it would be difficult to maintain the lane lines.


### 3. Possible Improvements for the Pipeline

A possible improvement would be to add a function that predicts where the road will go. This would make it easier for determinng future lane lines if the road is curved. 
