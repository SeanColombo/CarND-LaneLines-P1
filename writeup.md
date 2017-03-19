#**Finding Lane Lines on the Road** 

##Writeup Template

###You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)
[image1]: ./examples/grayscale.jpg "Grayscale"

---




### Reflection
###1. My pipeline operates on an already loaded image - so that it works independently of whether the item was loaded from a file, video-stream, etc..


###2. The pipeline is contained in the `process_image()` function and consists of 6 main steps:
1. Convert the image to grayscale.
2. Apply a Gaussian blur to smooth out the image.
3. Use Canny edge-detection to find edge-regions in image
4. Mask out a trapezoidal area of the image so that only the bottom-center of the image is considered.  The size of the trapezoid is configurable, and I found a sweet-spot with a trapezoid height of 44% of the image height.
5. Apply Hough transform to the edges detected by canny, to turn the edge-dots into solid lines.  These lines were passed into `draw_lines()` to be turned into solid, red lines (more on `draw_lines()` below).
6. The solid red lane-lines were then composted onto the original image (with `weighted_img()` so that the lines are semi-transparent and we can see where the actual line-paint on the road is underneath).

###3. `draw_lines()` had to be modified in several ways to fit our needs.  First, we had to average out all of the lines to create a single left and single right lane-line. To separate which segments belonged to which lane-line, I examined the slope. Negative slope all belonged to the left lane-line (this may be counter-intuitive, but remember that low y is at the top of the image) and positive sloped segments belonged to the right line.


TODO: FINISH WRITEUP.. WILL JUST MODIFY IT RIGHT IN GITHUB SO I CAN PREVIEW THE MARKDOWN

If you'd like to include images to show how the pipeline works, here is how to include an image: 

![alt text][image1]


###2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


###3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...