# **Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My image processing pipeline consisted of:

1. Convert to grayscale using c2.cvtColor().
1. Blur the image using cv2.GaussianBlur() with a kernel size of 3x3.
1. Detect edges using cv2.Canny(). Through experimentation with the example images and video, I found that using thresholds of 150, 300 gave the best results.
1. Mask off the region of interest using cv2.fillPoly() and cv2.bitwase_and(). My mask is a triangle going from the bottom left and bottom right corners to a point approximately in the middle of the image.
1. Extract line segments using cv2.HoughLines(). I found the best results with rho=1, theta=pi/180, threshold=35, minLineLength=100, maxLineGap=100.
1. Draw the line segments as red lines on a blank/black image using cv2.line().
1. Combine the annotated image with the original using cv2.addWeighted(). I found that the lines stood out more if I gave the line image an opacity of 1.0 and the original image an opacity of 0.7.

In order to draw a single line on the left and right lanes, I modified my pipeline by ...

1. Categorize line segment into whether they are a part of the left or right lane marker using their slopes, ignoring any lines that are approximately horizontal. If the slope is negative, we consider it to be a part of the right lane marker, otherwise we consider it to be a part of the left lane marker.
1. For left and right, determine the slope and intercept by taking the average of the slopes and intercepts of their line segments.
1. Use the slope and intercept to determine the points for left and right lines starting at the left side of the image and going all the way to the right side.
1. Draw the left and right lines using cv2.line().
1. Mask off the lines using the region of interest as described above.

### 2. Identify potential shortcomings with your current pipeline

- The masked regions assume that the camera is mounted perfectly dead center and the car is driving down a relatively straight road. I assume it would have trouble dealing with quick curves or hills.
- Using the slope to categorize our line segments as left or right assumes that we are only ever going to see one line on the left and/or one line on the right.
- Many of the parameters chosen are hand-chosen but depend on the size of the input image. For example, the HoughLines maxLineGap, minLineSize and threshold would need to be adjusted for larger/smaller input sizes.
- Each line segment contributes equally to the average slope and intercept, even though we have greater confidence in the longer lines.
- Our pipeline does not deal smoothly with curves.
- If the dotted lane marker become too sparse, we loose it entirely.


### 3. Suggest possible improvements to your pipeline

- Use machine learning to determine the best parameters for our Blur, Canny and HoughLines functions across different conditions.
- Apply weights to each line segment's contribution to the average slope intercept based on the line segment's length.
- Use a technique that can account for curved lines.
- Determine where the missing lane markers would likely be when only one side is present.
