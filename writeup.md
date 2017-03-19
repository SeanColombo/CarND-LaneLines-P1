# **Finding Lane Lines on the Road** 

## Reflection

### Pipeline
My pipeline operates on an already loaded image - so that it works independently of whether the item was loaded from a file, video-stream, etc..

The pipeline is contained in the `process_image()` function and consists of 6 main steps:
  1. Convert the image to grayscale.
  2. Apply a Gaussian blur to smooth out the image.
  3. Use Canny edge-detection to find edge-regions in image
  4. Mask out a trapezoidal area of the image so that only the bottom-center of the image is considered.  The size of the trapezoid is configurable, and I found a sweet-spot with a trapezoid height of 44% of the image height.
  5. Apply Hough transform to the edges detected by canny, to turn the edge-dots into solid lines.  These lines were passed into `draw_lines()` to be turned into solid, red lines (more on `draw_lines()` below).
  6. The solid red lane-lines were then composted onto the original image (with `weighted_img()` so that the lines are semi-transparent and we can see where the actual line-paint on the road is underneath).

`draw_lines()` had to be modified in several ways to fit our needs.  First, we had to average out all of the lines to create a single left and single right lane-line. To separate which segments belonged to which lane-line, I examined the slope. Negative slope all belonged to the left lane-line (this may be counter-intuitive, but remember that low y is at the top of the image) and positive sloped segments belonged to the right line.
   
   Once the line segments were separated, the top and bottom points of each line were totaled, then divided by the number of line-segments to get an average top and bottom point (ie: an average line).
   
   The average left and right lines were then extended using basic linear-algebra: we took the minimum and maximum y values of the trapzeoid and use the intersection-of-two-lines equation to figure out the x value at each of those points. These new intersections are the extents of the full lane-lines that we desired.

### Potential shortcomings of the current pipeline:
   There are several potential shortcomings. Some which were already evident, and others that can be inferred.
   
   One visible issue is that our settings of the Hough Transform would cause occasional frames to have no valid lines found for one of the lines (the dashed line, since it had less information).  We were able to minimize this to be only about 5 frames between both videos, but that's not a great failure rate.  One way to mitigate this would have been for us to memorize the previous values of each line, and when one line had no data, to temporarily use the data from before.  We could also set a limit on how many frames in a row that would be valid for, before we would consider the lane-line to be unknown.  This mirrors human behavior where we could easily predict where a lane is based off of where it was (if for example, the lane became covered by snow) but our confidence in our prediction would decrease the longer we remained without data.
   
   Another issue (that can be seen in the Challenge video output, which we don't consider to be handled yet) is that curved lane lines don't work very well.  One potential solution would be to use best-fit of a curve instead of a single line. An approach would be to see if there is a Hough-like transform for obtaining curves, but if not... we could attempt to still operate on the line-segments from Hough and just have draw-lines fit the line-segments to a curve instead of a single average line.  Another approach would be to divide our trapazoid up into several zones (probably 3) and detect lane-lines in each of these zones. The lane-lines could then likely be expressed by three straight lines per lane-line, which themselves would show the curvature of the lane.  Those three lines could even be smoothed to connect with each other.
   
   A third limitation (not exercized by this test data-set) is that this is a fairly fragile system with regards to weather and lighting. Any change in lighting would drastically weaken the code's ability to find lane-lines.  One potential solution (I'm just making this up, so I'm not sure if prior experimentation has found this to be workable) would be to look at frames in which no line-segments are detected and tweak the input-parameters in several directions (to be appropriate for low-lighting, snow, etc.) until one set of parameters gives reasonable results for the frame.
