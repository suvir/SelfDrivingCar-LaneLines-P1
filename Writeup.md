# **Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # "Image References"

[image1]: ./image_with_lines.png "Sample output"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

This was a great project. It was challenging because I it was my first time using OpenCV. It was a great learning experience and it was really cool 

The final output image can be seen below. It was achieved by applying the following steps:

![alt text][image1]

* **Smooth the image using Gaussian Blur**. 

This helps smooth the image and get rid of tiny variations in the image.

* **Canny edge detection**

Edges in the image are detected using Canny edge detector. 

* **Apply a region of interest selection**

This helped exclude all regions of the image that are not likely to require lane detection.
I chose a trapezium shaped region that included the lane lines.

* **Hough transform**

I used the PPHT (Progressive Probabilistic Hough Transform) implementation available in OpenCV to transform the edges in image to Hough Space. The **cv2.HoughLinesP()** function allows for 2 extra parameters that were quite useful - minLineLength and maxLineGap. When building the pipeline, I found that minLineLength was helpful in excluding very small line segments. maxLineGap was helpful in achieving long straight lines because it can be used to join line segments with gap between them. 

* **Extrapolate output of Hough Transform to compute the entire line**

The output of Hough transform was a series of lines. To convert these into two long straight lines, I processed each image as follows :

- Partition all lines in an image into 2 sets. Partitioning was done on the basis of slope values. I chose 2 intervals: m < -0.3 and m >= 0.3
- For each set of lines, use linear regression to find a least squares polynomial fit. I did this using Numpy polyfit. 
- Linear regression - **np.polyfit(..)** - helped determine the slope and intercept of left and right lane lines. 

* **Super-impose these lines onto original image**

* **Miscellanous**

I found that after sufficiently tuning parameters, grayscale conversion actually degraded performance on challenge video. I excluded grayscale computation for this reason. 


### 2. Identify potential shortcomings with your current pipeline

* The lane detection logic depends on actual straight white lines on the road. This will fail in cases like roads with poorly marked lanes or roads with cat's eye reflective markings. 
* The region of interest was calculated manually. I dont think it will generalize very well to all cases. Some cases that I can think of where it might run into issues:
- Images containing very sharp turns.
- Images of driving on roads with steep inclines (uphill or downhill)
* Time of day would sufficiently alter the color characteristics to break this. I dont think this pipeline will do very well on images of driving in the night.


### 3. Suggest possible improvements to your pipeline

* **Parameter Tuning** : Several parameters had to be tuned manually. This was primarily trial and error and likely means that the algorithm *overfits* this dataset. I would love to find the equivalent of cross validation from machine learning for image processing. 

* **Color filters** :  Adding common-sense logic like lane colors. We know that lane markings can only be white or yellow. Adding such information to the algorithm might help reduce the dependence on the *region of interest* computation and make the pipeline more robust. 

* **Jitter** : The lane lines flicker a bit when rendering on the video. In the challenge video, it randomly draws an incorrect lane line in a few frames. This could be resolved by using a more sophisticated line fitting algorithm. This would include excluding outliers and being able to fit the lane lines better.