# **Finding Lane Lines on the Road**

## Writeup

---

**Finding Lane Lines on the Road**

The goals/steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[results]: ./examples/basic_results.png "Results"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

#### Pipeline
My pipeline consisted of 5 steps:

##### 1. Color Space Change
At first, I was just changing from RGB to Grayscale, but it was very hard to make it work on the challenge, especially when there were yellow lines under the sun. So I decided to experiment with other colour spaces that could enhance the difference. I end up using HSV, at first using only the V channel as a "grayscale", but the hole HSV generates better results, the H channel help yellow line detection.

##### 2. Gaussian Blur
I used the gaussian blur to suppress some low-level noise.

##### 3. Canny
I used Canny for edge detection when used on HSV image, OpenCV's canny take the biggest gradient of all channels for each pixel. As it detects "any" edge, it gives a very noisy output for images with shadows and trees.

##### 4. Region of interest
The region of interest is a trapezoid, from the bottom of the image up to 40% of its hight. After taking on the challenge, I had to add colour filtering to "focus" on the right lines.

##### 5. Hough Lines
Finally, the Hough Transform is applied to find the lines on the edges. I had to increase sensitivity (reduce minimum line size) to be able to capture the smaller lines that appear on certain frames in the challenge, that generated some "intra-line segments" (segments inside the lane lines) that I had to deal inside the draw_lines.

#### Function draw_lines()

I experimented with many different approaches to make the draw_line function (averaging theta/rho, averaging theta/intercept, histogram 2d filtering, a second hough transform from the line segments, and more). The major issue was to consider the outliers that appeared after increasing the overall sensitivity to find lines in the challenge. To counteract the presence of outliers, I decided to first find the expected end-points of a given segment (in the middle and bottom of the image), then average only the point within a certain distance from the median (measured based on the mean absolute error). I do it for each lane line "side" by separating lines according to increasing or decreasing.

Here is the output for each step on the test images (including some screenshots from the challenge):

![alt text][results]


### 2. Identify potential shortcomings with your current pipeline

One shortcoming is that depending on how spaced or thick the line is, the "single line" can "vibrate" inside the true lane line.
Another problem is that is it might not work at all for non-straight lines and will not show anything for missing lines (not temporal consistency).


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to add a simple filter to account for temporal consistency.

Another potential improvement could be to try to understand the lane line "contour" as a single object, and maybe try to fit circle arcs instead of straight lines to account for curves.