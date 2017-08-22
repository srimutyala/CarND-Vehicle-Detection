**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/samples.png
[image2]: ./output_images/HOG_features_example.png
[image3]: ./output_images/vehicle_detect.png
[image4]: ./output_images/vehicle_detection_scales_example.png
[image5]: ./output_images/vehicle_detection_boxes_example.png
[image6]: ./output_images/heatmap.png


## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

I started by reading in all the `vehicle` and `non-vehicle` images. I used all of the training data provided for this project. Below is a sample of car and non-car images

![alt text][image1]

The pipeline for vehicle detection was straight forward for the most part once I followed along the course module. The challenging portion of it is the selection of the parameters. So, I created a skeleton pipeline with variable for all of the parameters.

Then, I created a notebook to train the model across all interesting color spaces (RGB, YCbCr, HSV, HSL, YUV), first with each channel seperately and then with the color image for each space using the same set of HOG parameters, historgram and spatial binning parameters. I realised the color spaces HSL & HSV did not do a very good job. After narrowing the choices to RGB, YCrCb, YUV, I saw quite a few false postives. It took a while to figure out that the historgram and spatial binning fetaures were causing this. I think with proper tweaking of these parameters I could reduce these false postives. But, I decided to see how the pipeline performs if I just went with HOG. Surprsingly, it performed well. It also led me to believe the color spaces YUV and YCrCb are better suited for this without a lot of filtering. YCrCb provided even less false positives but at the expense of some sensitivity. In the end I decided to go with YCrCb space. 


![alt text][image2]

####2. Explain how you settled on your final choice of HOG parameters.

After this, I trained my model on different combinations of HOG parameters particularly on orientation, pix_per_cell & cells_per_block. I used a range of 8-12 for orientation, 4-16 for pex_per_cell & 2-4 for cells_per_block. I found that the values used in the course module worked ok for an initial attempt and in the end, just chnaging the ore=ientation from 9 to 11 seemed to be the only change required.

####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using the HOG features extracted as mentioned above. I did not have to scale the features as I am just using a single type of it.

###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

The test images worked well with the default Y positions for searching. But, in a video/real-time, cars can be at any distance from the camera and so can appear small or large. All of our training data are at a particular scale. So, we need to run the detection pipeline on various sized windows along with picking the appropriate scale for it based on the window size differential.

![alt text][image3]
![alt text][image4]

####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using YCrCb 3-channel HOG features which provided a nice result.  Here is an example

![alt text][image5]
---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video.mp4)



####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video. I created a class to store the locations of the boxes. Each new frame that's processed goes through this class's box history and if it exceeds a threshold, I remove the oldest boxes and apply thresholding to their heat maps. This reduces flase postives and gives a better detection.

![alt text][image6]

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Figuring out fetaures & parameters is still a guessing game. There needs to be a better way to arrive at these parameter values. The project video is the regular video file that I have used in the past. It would be intersting to see how well the pipeline behaves under challenging road & weather conditions.

