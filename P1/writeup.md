# **Finding Lane Lines on the Road**

The goal of this project is to create a pipeline to find the lane lines on the road.

## Pipeline

The pipeline to detect and draw the lane lines on the image consists of 7 steps.

1. Convert the image to grayscale.
2. Remove noise/unnecessary edges from the image by blurring the image using gaussian blur function.
3. Detect edges in the image by applying canny transform.
4. Remove the edges outside of the region of interest in the image.
5. Find the lines based on the edges by applying hough transform.
6. Draw the lines on a blank image.
7. Draw the lines image on top of the original image.

In the fist step, we convert the image to grayscale 8-bit image. The is required for canny transform. The opencv 'cvtColor' function is used to do the conversion.

In the second step, we blur the image using opencv 'GaussianBlur' function with kernel size of 5x5.

In the third step, we detect the edges in the image using canny transform. The opencv 'Canny' function is used to do the edge detection. It works by calculating the intensity gradient of the image, then removing the non-edge pixels. It accepts 'low_threshold' and 'high_threshold' values as arguments. The pixels with gradient values above the high threshold are detected as edges. The pixels with gradient values between the low threshold and the high threshold values are accepted. The low threshold value of 50 and high threshold value of 150 are found using experimentation.

In the fourth step, we drop the edges outside the area of interest. The vertices for the region of interest depends on the field of view of the camera.

For the test images and videos, the following vertices are determined using experimentation:

> (0, image-height), (460, 325), (510, 325), (image-width, image-height)

In the fifth step, we detect the lines by applying hough transform. the opencv 'HoughLinesP' function is used to detect the lines. The hough transform works by representing the lines (y=mx+b) in polar coordinate system (rho=xcos0+ysin0) where rho is the length of the line connecting the origin to the closest point of the line being represented and theta is the angle between the x-axis and the line connecting the origin to the line being represented.

At high level, hough function works like this. For each x and y, if we plot the set of lines that goes through it in hough space, we get a sine curve. The sine curve of all the points that are part of a line will intersect at a certain rho and theta. The minimum number of intersection required to detect a line is specified with the parameter called 'threshold'. The minimum number of points to form a line is specified with a parameter called 'minLineLength' and the maximum gap between the two points within the same line is specified with a parameter called 'maxLineGap'.

For the test images and videos, the following values are determined using experimentation:

> rho = 2, theta = 1 degree, threshold = 20, minLineLength = 10, maxLineGap = 10

In the sixth step, we draw the lines detected by hough transform on a blank image. The improved draw_line function works like this.

1. First we separate the left lane line and the right lane line. If the slope of the line (y2-y1)/(x2-x1) is less than 0, it is part of the left lane and if the slope is greater than 0, it is part of right lane. We use a slope threshold value of 0.5 instead of 0 to remove certain stray lines.
2. Next, in order to draw the line, we determine the average slope and y-intercept of all the lines in each lane using numpy's polyfit function of degree 1.
3. The top x value is the maximum x value for the left lane line and minimum x value for right lane line.
4. The top y is calculated using y=mx+b.
5. The bottom y should be equal to the height of the image since we have to extrapolate the line to the bottom of the image.
6. The bottom x is calculated using x=(y-b)/m.
7. Next the line is drawn using opencv 'line' function.

The seventh and final step of the pipeline combines the line drawn on the blank image and the original unprocessed image using opencv 'addWeighted' function with certain transparency.

The pipeline executed on the test images produced the following images.

![White Curve](/test_images_output/solidWhiteCurve.jpg)
![White Right](/test_images_output/solidWhiteRight.jpg)
![Yellow Curve](/test_images_output/solidYellowCurve.jpg)
![Yellow Curve 2](/test_images_output/solidYellowCurve2.jpg)
![Yellow Left](/test_images_output/solidYellowLeft.jpg)
![White Car Lane Switch](/test_images_output/whiteCarLaneSwitch.jpg)

## Shortcomings of the pipeline

* The pipeline is too slow for the real-world use case.
* It will not work reliably in different lane and road colors.
* It will not detect lane lines correctly on curvy roads.
* It could detect other objects on the road as a lane line.
* It will not work reliably in different light and weather conditions.

## Possible improvements to the pipeline

* The draw line function could be improved for speed.
* The edge detection parameters could be improved to detect edges correctly on lanes and roads with different colors.
