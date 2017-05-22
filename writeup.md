**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/car_noncar.jpg
[image2]: ./output_images/HOG_example.jpg
[image3]: ./output_images/slide_windows.jpg
[image4]: ./output_images/more_windows.jpg
[image5]: ./output_images/heat_maps.png
[image6]: ./output_images/gray.png
[video1]: ./project_video_output.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in code cells from 4th to 6th of the IPython notebook. 

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

Here is an example for images above using the `YUV` color space and HOG parameters of `orientations=11`, `pixels_per_cell=(16, 16)` and `cells_per_block=(2, 2)`:

![alt text][image2]

####2. Explain how you settled on your final choice of HOG parameters.

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I used the "trial and fail" method and tried different combination before I decided to use `YUV` color space and HOG parameters of `orientations=11`, `pixels_per_cell=(16, 16)` and `cells_per_block=(2, 2)`

####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using only the HOG features. As a result, I did not use `StandardScaler()` to fit the data. The code is in the 8th and 12 th code cells.

###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I decided to only search cars in the region where cars possibly appear (`ystart = 400, ystop = 650`) with different scale of windows (original window is 64 x 64). The code is in code cells from 13th to 19th. The scales are picked up by eye balling. The biggest windows I would search is 160 x 160.

![alt text][image3]

####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on four scales using only YUV 3-channel HOG features in the feature vector, which provided a nice result. Then I use the heat map to remove some false postives. Here are some example images:

![alt text][image4]
---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video_output.mp4)


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the bounding boxes in recent frames of the video with max number of 15.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from test_images folder, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here are the resulting bounding boxes of four test images and their corresponding heatmaps:

![alt text][image5]

### Here is a output example of `scipy.ndimage.measurements.label()` on the integrated heatmap from the first test image:
![alt text][image6]

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  

First, I trained a SVM with HOG features to identify cars from surroundings. Then, I used the sliding window technique to find the cars with the SVM just trained. The bounding boxes are added to a heatmap and a threshold is used to remove some false postives. The final bounding boxes is from `scipy.ndimage.measurements.label()`.
d
The differences of `plt.imread` and `cv2.imread` take me a lot of time to make them consistent with each other. My pipeline is likely fail when the cars are far away as the smallest window size is 64 x 64, which is too big to identify the boundary of a car. In addtion, the cars on the oncoming lane disturb the result. It could be improved by remove the oncoming lane when searching cars.
