#**Traffic Sign Recognition**


---

**Build a Traffic Sign Recognition Project**

The goals / steps of this project are the following:
* Load the data set (see below for links to the project data set)
* Explore, summarize and visualize the data set
* Design, train and test a model architecture
* Use the model to make predictions on new images
* Analyze the softmax probabilities of the new images
* Summarize the results with a written report


[//]: # (Image References)

[image1]: ./writeup/signs.jpg "Images from the training dataset"
[image2]: ./writeup/labels_histogram.png "Label distibutions."
[image3]: ./new_signs_testing/1.png "Traffic Sign 1"
[image4]: ./new_signs_testing/2.png "Traffic Sign 2"
[image5]: ./new_signs_testing/3.png "Traffic Sign 3"
[image6]: ./new_signs_testing/4.png "Traffic Sign 4"
[image7]: ./new_signs_testing/5.png "Traffic Sign 5"
[image8]: ./new_signs_testing/6.png "Traffic Sign 6"
[image9]: ./new_signs_testing/7.png "Traffic Sign 7"

** Rubric Points
*** Here I will consider the [rubric points](https://review.udacity.com/#!/rubrics/481/view) individually and describe how I addressed each point in my implementation.

---
## Writeup / README

You're reading it! and here is a link to my [project code](https://github.com/mbhavya/CarND-Traffic-Sign-Classifier-Project/blob/master/Traffic_Sign_Classifier.ipynb)

## Data Set Summary & Exploration

Data exploration:

I used the some basic output to determine summary statistics of the traffic
signs data set:

* Size of training set        34799
* Size of validation set      4410
* Size of test set            12630
* Shape of traffic sign image 32x32x3
* No. unique classes in the data set 43


Here is a visualization of an excerpt of the data set images:

![Training set][image1]

The histogram shows that the distribution of labels in the training and validation set are very similar:

![Label distribution][image2]

## Design and Test a Model Architecture

Preprocessing:

Color is a distinctive feature for traffic signs, so conversion to greyscale would have lead to loss of information, hence I didn't do that in the pre-processing step. There are three dominant colors used in traffic signs: Blue, red and yellow, and usually only one (or no) dominant color per sign. So only the dominant color is already a feature that allows to categorize a sign into four different groups (including one for no color at all).

There are significant differences in image brightness, so I decided to normalize the images with respect to their maximum value. By normalizing with the maximum value, the overall brighness becomes more homogenous throughout the image sets. This makes classification easier since brightness should not be a distinctive feature of any traffic sign. Traffic signs are always high contrast, reflective and as bright as possible for good visibility.

Augmentation:

The signs in the images are not necessarily aligned properly, some are rotated quite significantly. So I decided to add randomly rotated copies to the training set. For the rotation angle I chose a uniform distribution between -6 and +6 degrees. Higher rotation angles produced led to a decrease in detection accuracy.

Flipping is not a good choice for augmentation, as this could even change the meaning of a sign.

Model Architecture:
My final model consisted of the following layers:

| Layer         		|     Description	        					|
|:---------------------:|:---------------------------------------------:|
| Input         		| 32x32x3 RGB image   							|
| Convolution 5x5     	| 1x1 stride, valid padding, outputs 28x28x8 	|
| Leaky RELU					|												|
| Dropout					|												|
| Convolution 5x5     	| 1x1 stride, same padding, outputs 28x28x16 	|
| Leaky RELU					|												|
| Dropout					|												|
| Max pooling	      	| 2x2 stride,  outputs 14x14x16 				|
| Convolution 5x5     	| 1x1 stride, valid padding, outputs 10x10x32 	|
| Leaky RELU					|												|
| Dropout					|												|
| Max pooling	      	| 2x2 stride,  outputs 5x5x16 				|
| Flatten				|												|
| Fully connected		| 800x240        									|
| Leaky RELU					|												|
| Dropout					|												|
| Fully connected		| 240x120        									|
| Leaky RELU					|												|
| Dropout					|												|
| Fully connected		| 120x43        									|
| Leaky RELU					|												|
| Dropout					|												|
| Softmax				|         									|
| Output Class 				|									|


## Training
I chose a learning rate of 0.001. Initialized the variables with the Xavier initializer. Adam Optimizer on cross entropy was done for optimization. A batch size of 512 and 12 epochs turned out to be an efficent choice.

## Model Improvement
I started with a very basic architecture close to the original LeNet. The first results were very inaccurate and experimenting with hyperparamters didn't garner enough improvement.

Changing image normalization to the apporach which uses the image maximum instead of 255 improved the results.
Next I added multiple layers to make the network non-linear, so it can "understand" images better and give accurate results. Adding batch normalization caused a decrease in accuracy.

I removed batch normalization which gave a huge improvement.
The training accuracy was still significantly higher than the validation accuracy, so I decided to increase the model complexity by adding an additional convolutional layer.

My current results are:

Training accuracy: 1.000
Validation accuracy: 0.956
Test accuracy: 0.944

## Test a Model on New Images

![sign 1][image3] ![sign 2][image4] ![sign 3][image5]
![sign 4][image6] ![sign 5][image7] ![sign 6][image8]

The images are all of high quality, so the detection results are good and the top softmax probability for almost all estimates was very high (>=90% in all cases) and all but one estimates are correct.

This is higher than the accuracy obtained on the test set mostly for two reasons:
* The test accuracy is at 94%.
* The error range of the new estimate is 85.7% with six images. The decrease in percentage is caused by a single image for which the network's accuracy was 53%. The overall image quality of the new set is much higher.
