#**Finding Lane Lines on the Road** 

[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

##**My solution approach and reflections**

I tried out the following techniques to join lane segments after canny detection, region masking and hough transformation of a video frame:

1. Detect the left and right lane top most and bottom most (x,y) point pairs and join them in a single line for each lane.
2. For each lane, gather a series of points from all the line segments and use opencv fitLine that minimizes the squared error of points.
3. Compute the average slope and intercept of all the line segments that make up a lane and use that to draw a single lane line.

Approach 3 worked for me the best. But the lanes seem to jump around too much from frame to frame in the test videos. So I tried the following two additional techniques:

1. Limit the percentage change in slope of a lane line between each successive video frame to a tunable paramater value.
2. Use a running exponentially weighted moving average to "smooth" out sudden changes in a lane line's slope.

Approach 2 gave me the best result as shown in the video.

When I moved on to trying my pipeline on the challenge video I faced several challenges as listed below:

*Challenge 1: Detecting lane lines against a light concrete background instead of black asphalt and also when there were shadows on the road ahead*

The grayscale image with gaussian blur applied to a video frame basically washed out the pixel intensity difference between the background and the lane lines. I tried the following two approaches to overcome the problem:

1. a. I adjusted the low and high thresholds of the Canny edge detection algorithm to pick out very little pixel intensity differences. This worked for the video frames where the lanes lines were against concrete but resulted in a lot of spurious edges for the regular case. I figured that the Canny parameters need to be determined dynamically based on the current frame. However, I did not know of a way of doing that.
1. b. I converted the image to HSV scale instead of grayscale and filtered out different shades of white and yellow pixels and applied Canny edge detection on the resultant image with the same thresholding parameters used for all the other videos.

Approach 1.b gave me the best result. However, I realize that this approach may not work ideally when the lane lines are or appear to be of different colors due to environmental conditions (such as night or the presence of snow etc.). On browsing the facebook page for the December 2016 class, I learned of two possible techniques that could be used to deal with this problem: (i) Random Sampling Consensus (RANSAC) and (ii) Kalman Filtering. I spent some time learning about the intuition behind both of those techniques so that I might be able to use it in a future project.

*Challenge 2: Lanes with high curvature*

On image frames where the lanes had high curvature, the extrapolated straight lane lines appearing somewhat tangential to the curvature of the actual lane line. Some of these artifacts could perhaps be seen in my output for the challenge video. The only option I could think of was to perhaps fit a curve with higher order polynomials intead of a straight line.

##**Finished Videos**

![](https://github.com/mukilk7/CarND-LaneLines-P1/raw/master/finished/p1_white.gif)

![](https://github.com/mukilk7/CarND-LaneLines-P1/raw/master/finished/p1_yellow.gif)

![](https://github.com/mukilk7/CarND-LaneLines-P1/raw/master/finished/p1_challenge.gif)
