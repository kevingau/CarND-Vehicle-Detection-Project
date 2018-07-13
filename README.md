# Vehicle Detection
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

### Goals

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

Here are links to the labeled data for [vehicle](https://s3.amazonaws.com/udacity-sdc/Vehicle_Tracking/vehicles.zip) and [non-vehicle](https://s3.amazonaws.com/udacity-sdc/Vehicle_Tracking/non-vehicles.zip) examples to train your classifier.  These example images come from a combination of the [GTI vehicle image database](http://www.gti.ssr.upm.es/data/Vehicle_database.html), the [KITTI vision benchmark suite](http://www.cvlibs.net/datasets/kitti/), and examples extracted from the project video itself.

[//]: # (Image References)
[image1]: ./output_images/01.png 'Car and Not-car'
[image2]: ./output_images/02.png 'HOG Visualization'
[image4]: ./output_images/04.png 'Sliding Window'
[image5]: ./output_images/05.png 'bboxes_and_heat'
[image6]: ./output_images/06.png 'labels_map'
[image7]: ./output_images/07.png 'output_bboxes'
[video1]: ./project_video_output.mp4
  
---

### Histogram of Oriented Gradients (HOG)

#### 1. Extracted HOG features from the training images.

The code for this step is contained in the fifth code cell of the [IPython notebook](./Vehicle-Detection.ipynb).  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

Then, I explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YUV` color space and HOG parameters of `orientations=11`, `pixels_per_cell=(16, 16)` and `cells_per_block=(2, 2)`:


![alt text][image2]

#### 2. Settled on the final choice of HOG parameters.
 
I tried various combinations of parameters. For example, RGB, HSV, LUV, HLS, YUV, and YCrCb in color space and different hog channel. Finally, I found that `color_space = 'YCrCb'`, `hog_channel = 'ALL'`, `orient = 9`, `pix_per_cell = 8`, `cell_per_block = 2` performed well.

#### 3. Trained a classifier using your selected HOG features.

The code for this step is contained in the sixth and seventh code cell of the IPython notebook.  

Initially I read all the car and non-car images using glob.glob().

Then I extracted all features using function extract_features_standard. The function only uses HOG features to form an array of feature vectors.

Then I performed a split on training and testing data using train_test_split from sklearn.model_selection.

I trained a linear SVM using LinearSVC from sklearn.svm.

### Sliding Window Search

#### 1. Implemented a sliding window search.

I decided to search random window positions from 64x64, 96x96, 128x128, to 160x160

#### 2. Showed some examples of test images to demonstrate how the pipeline is working. 

Ultimately I searched on two scales using YCrCb 3-channel HOG features to optimize the performance of my classifier. Here are some example images:

![alt text][image4]
---

### Video Implementation

#### 1. A link to the final video output.

Here's a link to my [video result](./project_video_output.mp4)


#### 2. Implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video. From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### The image of heatmap:

![alt text][image5]

### The output of `scipy.ndimage.measurements.label()` on the heatmap:
![alt text][image6]

### The resulting bounding boxes are drawn onto the frame:
![alt text][image7]
