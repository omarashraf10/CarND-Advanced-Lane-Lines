## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

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


### Camera Calibration

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort() 

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:

![screenshot - 11_3_2018 8_35_41 pm](https://user-images.githubusercontent.com/33129729/47956150-f0aa6a00-dfa8-11e8-9356-b732facf55fe.png)


#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image by applying sobelx and combine it with the s channel
of the image 

combined_binary = np.zeros_like(sxbinary)
combined_binary[(s_binary == 1) | (sxbinary == 1)] = 1

Here's an example of my output for this step.  (note: this is not actually from one of the test images)

![screenshot - 11_3_2018 8_24_06 pm](https://user-images.githubusercontent.com/33129729/47956193-5ac30f00-dfa9-11e8-9ad1-a7b18736a229.png)


#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

i applyied prespective transform of each image to turning it to the bird eye view by using this function cv2.getPerspectiveTransform(src, dst)
to generate the M matrix and inverse M matrix
i used these values to my src and dis points:-

```python
src = np.float32([[585,450], [200,710],[1130,720],[710,460]])
dst = np.float32([[145,0],[145,720],[test_images[0].shape[1]-145,720],[test_images[0].shape[1]-145,0]])
```

This resulted in the following source and destination points:

| Source        | Destination         | 
|:-------------:|:-------------------:| 
| 585, 450      | 145, 0              | 
| 200, 710      | 145, 720            |
| 1130, 720     | width-145, 720      |
| 710, 460      | width-145, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![screenshot - 11_3_2018 8_24_45 pm](https://user-images.githubusercontent.com/33129729/47956252-3ca9de80-dfaa-11e8-865b-c30b7d5f3d5a.png)


#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

then i used fit_polynomial , find_lane_pixels and measure_curvature_real functions to take each wraped image and

Take a histogram of the bottom half of the image and Find the peak of the left and right halves of the histogram then

generate left_lane_inds and right_lane_inds then i measured the curvature and the distance from the centre  by the function 

measure_curvature_real and then draw all the points between the two lanes with green by this function:

cv2.fillPoly(color_warp, np.int_([pts]), (0,255, 0))



![screenshot - 11_3_2018 8_25_46 pm](https://user-images.githubusercontent.com/33129729/47956343-a676b800-dfab-11e8-98e2-68dadb537dd3.png)


then i unwraped the image by using the Minv matrix

![screenshot - 11_3_2018 8_26_18 pm](https://user-images.githubusercontent.com/33129729/47956345-b7bfc480-dfab-11e8-8857-92fc33fb9d30.png)



#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

the final shape of the image after using cv2.addWeighted function :

![screenshot - 11_3_2018 8_26_46 pm](https://user-images.githubusercontent.com/33129729/47956349-c3ab8680-dfab-11e8-9ce9-f8301d388f25.png)


### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

i applyied previous steps to each frame of the video and writing the radious of curvature and the distance from the center


Here's a link to my video result 

https://drive.google.com/file/d/1zy-fnHBEzMzBZhkOsl93XXRNFqyD33OS/view?fbclid=IwAR1S2q5Z4F5KuOv3L8NMcEG0ptXDdzk5vHWymgTuZ1M1gD2_mVkG3ZPM0Xo

