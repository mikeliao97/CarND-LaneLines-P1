# **Finding Lane Lines on the Road** 
---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"
[image2]: ./results/solid-white-right.gif 
[image3]: ./results/solid-yellow-left.gif 
[image4]: ./results/challenge.gif 
[image5]: ./results/canny.png
[image6]: ./results/hough.png
[image7]: ./results/mask.png

---

### Reflection

### 1. The Pipeline
My pipeline consisted of 5 steps. 
1. Convert the images to grayscale for Canny Edge Detection
2. Apply a Gaussian Blur with a kernel size=5 to reduce detail and noise of the image
3. Apply canny edge detection to get promising edges
4. Use the edges from canny to get lines using Hough Transform in a given region of interest
5. With lines from Hough, combine the lines and process them to fit onto the lanes on the road.

## Results
### Exhibit 1: Solid White Right
![alt text][image2]

### Exhibit 2: Yellow/White Left
![solid yellow left][image3]

### Exhibit 3: Yellow/White Turn
![solid yellow left][image4]

To go into a little more detail regarding the pipeline. First, canny edge detection expects a grayscale image, and essentially thinks of edges as rapid changes of "light -> dark" or vice-versa.

![grayscale][image1]

We then apply a Gaussian filter to remove some noise and run it through the Canny Edge Detector. Two parameters for this function are **low_thresh** and **high_thresh**. Anything edges that don't meet the gradient threshold **low_thresh** are disregarded and anything above **high_thresh** are automatically kept. The output looks like this:

![canny][image5]

With the edges, we now want to find lines. Before that however, we set a prior to look only at the road. We do this by defining a quadrilateral mask that covers the road the car is on to reduce computation and increase accuracy.

![mask][image7]

Combining the mask with edges, we pass it to the hough transform to get lines, and the raw form looks like this:

![mask][image6] 

With all these lines, how do we actually find the accurate lines that surround our car? My approach is to separate the lines by slope -- (positive means the left lane, while negative means the right). Then, for each lane, I fit a polynomial y = ax + b. To draw a lane_line, you need two points. For one point, I take the point starting_xy (x,y) that is closets to the center image. For the 2nd point, I use the polynmial y = ax+b, with y being the size of the image height so solve for x.


### 2. Identify potential shortcomings with your current pipeline
A potential shortcoming my approach is that it is not fully generalizable. It works fine for Exhibit 1/2, but for Exhibit 3, I needed to modify the parameters for Hough Transform. Hough Transform takes a bunch of arguments such as (**treshold**, **min_line_len**, **max_line_gap**), that have a huge effect on the result.


### 3. Suggest possible improvements to your pipeline
I would continue experimenting with the differnt parameters for hough transform. Maybe use some grid search to ptimi