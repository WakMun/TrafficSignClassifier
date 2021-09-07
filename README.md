# **Traffic Sign Recognition** 

## Writeup

---

**Build a Traffic Sign Recognition Project**

The goal of the project was to design, build and train a model based on convolutional 
neural networks (CNN) which is able to classify german road signs.

The code for this project is provided in this [Jupyter notebook](https://github.com/WakMun/TrafficSignClassifier/blob/master/Traffic_Sign_Classifier.ipynb). The data that is used for training the network was provided by Udacity and is also available [here](http://benchmark.ini.rub.de/?section=gtsrb&subsection=dataset), however is not part of this repository. At the end of training the accuracy values were as follows:

| Data Set			        |     Accuracy	        					| 
|:---------------------:|:---------------------------------------------:| 
| Train      		| 1.00   									| 
| Test     			| 0.955 									|
| Validation					| 0.981											|




I achieved this using a modernized version of [LeNet-5 architecture](http://yann.lecun.com/exdb/lenet/) that was published by Yann Lecun in 1998. Already without any optimizations, this archtecture provides a pretty good basis for image classifications tasks with accuracy above 85%. With the addition of modern techniques such as regularization and RELU activations as well as preprocessing steps, this archtecture comfartably achieves higher than 95% accuracy on the provided data set.   

Summary of the steps of this project is as follows. 
* Load the data set (see below for links to the project data set)
* Explore, summarize and visualize the data set
* Design, train and test a model architecture
* Use the model to make predictions on new images
* Analyze the softmax probabilities of the new images

The details about each of these are provided below.


[//]: # (Image References)


[image1]: ./outImages/AccuracyPic.jpg "Accuracy per Epoch and most Error Prone Classes"
[image2]: ./outImages/afterPreprocess.jpg "Postprocessing Results"
[image3]: ./outImages/b4Preprocess.jpg "Before Postprocessing"
[image4]: ./outImages/DistributionOfLabels.jpg "Distribution of Input Data"
[image5]: ./outImages/InputVisual.jpg "Visulizing Input Data"
[image6]: ./outImages/predicHistoWeb.jpg "Predictions for Images from Web"
[image7]: ./outImages/webClassification.jpg "Classification of Web Images"
[image8]: ./outImages/webImages.jpg "Images Selected from Web"
[image9]: https://wikimedia.org/api/rest_v1/media/math/render/svg/5c591a0eeba163a12f69f937adbae5886d6273db

### Data Set Summary & Exploration

#### 1. Data Set in Numbers 

Using simple python's builtin functions I find the summary statistics as follows:

* Number of training examples = 34799
* Number of testing examples = 12630
* Number of validation examples = 4410
* Image data shape = (32, 32, 3)
* Number of classes = 43

#### 2. Exploratory visualization of the dataset
I explore the distribution of data in varous classes using a histogram chart where the frequency of each class is mapped.
![alt text][image4]

Classes are numbered from 0 to 42 (end points included). It is clear from the figure above that there are much more samples present for classes numbered at the lower end of this range than those at the top end. If the data was balanced the mean (shown as green line) would have been at the middle ie 21. This skew can result in the developed model not learning the signs belonging higher end of the range ie 21 to 42. To solve this potential issue more training data can be generated by transforming the samples belonging to higher end of the scale and duplicating these with small transformations such as rotating, aspect ratio change etc. To decide if this measure is needed or not, the accuracy of the model has to be seen.

This topic is further discussed below, along with the accuracy of the model.

#### 3. Classes Visualization
To get a feeling about the data, I plot 15 random images from each class as follows. It seems that data is clean and the provided images cleanly match up with their respective classes.
![alt text][image5]

### Design and Test a Model Architecture

#### 1. Preprocessing

As a first step, I decided to convert the images to grayscale. Although color in the image can provide useful information but various changing light conditions transform the color in the images so drastically that it does not seem a good idea to include it in the input. Also, it is helpful to reduce the inputs to minimum possible necessary features so that all "decisions" are based on features that really matter. Adding superfluous information like color can lead to the [curse of dimensionality](https://en.wikipedia.org/wiki/Curse_of_dimensionality). 

In the second step, I normalize the data using [mean zero scaling](https://en.wikipedia.org/wiki/Feature_scaling). The formula for this is given as follows:

![alt text][image9]

The benefits of normalization for machine learning are [well established](https://arxiv.org/abs/1502.03167). During the exploration phase a couple of other normalization techniques were also evaluated. These did not help much in the overall accuracy, but thier effect on the conviction of the correct prediction was quite significant. Ie for correct predictions, the probability was concentrated in the correct choice, whereas, for incorrect choices the resultant probabilities were spread over a number of choices.

Following table provides an overview of different techniques where I used the first one due to the zero mean and the range being within -1 to 1. 

| Technique 	| Max            	| Mean            	| Min             	| Standard Deviation 	|
|-----------	|----------------	|-----------------	|-----------------	|--------------------	|
| Mean Zero 	| 0.686836355063 	| 0.0             	| -0.313163644937 	| 0.262438084895     	|
| MinMax    	| 1.0            	| -0.324513108538 	| -1.0            	| 0.586109319525     	|
| z score   	| 2.61713674422  	| 0.0             	| -1.19328581849  	| 1.0                	|

As final part of preprocessing, the depth of dimensions of data were restored to four. One dimension was lost during grayscale conversion ie 32x32x3 to 32x32. 
So each individual image was reshaped to 32x32x1 to be in the valid format for CNN model.

Here ares some example of a images before and after preprocessing.

![alt text][image3]:

![alt text][image2]:



#### 2. Describe what your final model architecture looks like including model type, layers, layer sizes, connectivity, etc.) Consider including a diagram and/or table describing the final model.

My final model consisted of the following layers:

| Layer                      	| Input         	| Output   	| Description                                                    	|
|----------------------------	|---------------	|----------	|----------------------------------------------------------------	|
| Input                      	| 32x32x1       	| 0.0      	| Input normalized image in grayscale                            	|
| Convolutional Layer 1      	| 32x32x1       	| 28x28x16 	| Using a convolution on 5x5 with stride: 1x1 and padding: valid 	|
| Activation Layer (ReLU)    	| 28x28x16      	| 28x28x16 	| Activation using rectified linear unit                         	|
| Max Pooling layer          	| 28x28x16      	| 14x14x16 	| Using kernel size: 2x2, stride: 2x2 and padding: valid         	|
| Convolutional Layer 2      	| 14x14x16      	| 10x10x64 	| Using a convolution on 5x5 with stride: 1x1 and padding: valid 	|
| Activation Layer (ReLU)    	| 10x10x64      	| 10x10x64 	| Activation layer                                               	|
| Max Pooling Layer          	| 10x10x64      	| 5x5x64   	| Using kernel size: 2x2, stride: 2x2 and padding: valid         	|
| Flat Fully Connected Layer 	| 5x5x64 = 1600 	| 240      	| Fully connected layer for consolidating the found features     	|
| Activation Layer (ReLU)    	| 240           	| 240      	| Activation layer                                               	|
| Dropout                    	| 240           	| 240      	| keep probability = 0.6                                         	|
| Flat Fully Connected Layer 	| 240           	| 84       	| Fully connected layer for consolidation                        	|
| Activation Layer (ReLU)    	| 84            	| 84       	| Activation layer                                               	|
| Dropout                    	| 84            	| 84       	| Keep probability = 0.6                                         	|
| Output. Fully Connected    	| 84            	| 43       	| Output layer for one hot encoded output                        	|											|
 


#### 3. Training the Model. The discussion can include the type of optimizer, the batch size, number of epochs and any hyperparameters such as learning rate.

To train the model, I used an ....

#### 4. Describe the approach taken for finding a solution and getting the validation set accuracy to be at least 0.93. Include in the discussion the results on the training, validation and test sets and where in the code these were calculated. Your approach may have been an iterative process, in which case, outline the steps you took to get to the final solution and why you chose those steps. Perhaps your solution involved an already well known implementation or architecture. In this case, discuss why you think the architecture is suitable for the current problem.

My final model results were:
* training set accuracy of ?
* validation set accuracy of ? 
* test set accuracy of ?

If an iterative approach was chosen:
* What was the first architecture that was tried and why was it chosen?
* What were some problems with the initial architecture?
* How was the architecture adjusted and why was it adjusted? Typical adjustments could include choosing a different model architecture, adding or taking away layers (pooling, dropout, convolution, etc), using an activation function or changing the activation function. One common justification for adjusting an architecture would be due to overfitting or underfitting. A high accuracy on the training set but low accuracy on the validation set indicates over fitting; a low accuracy on both sets indicates under fitting.
* Which parameters were tuned? How were they adjusted and why?
* What are some of the important design choices and why were they chosen? For example, why might a convolution layer work well with this problem? How might a dropout layer help with creating a successful model?

If a well known architecture was chosen:
* What architecture was chosen?
* Why did you believe it would be relevant to the traffic sign application?
* How does the final model's accuracy on the training, validation and test set provide evidence that the model is working well?
 

### Test a Model on New Images

#### 1. Choose five German traffic signs found on the web and provide them in the report. For each image, discuss what quality or qualities might be difficult to classify.

I selected some signs out of these two videos. To reduce the bias of selection I selected first 6 signs that matched with our training classes out of each video.
* https://www.youtube.com/watch?v=QE03kKtJeWs
* https://www.youtube.com/watch?v=Hx44jrWHaUE


![alt text][image8]: Road signs extracted out of youtube videos.

Thier classification results are as follows. Out of 12 signs, 11 were classified correctly ie and accuracy of nearly 91%


![alt text][image7]: Images classification by the developed system

The first image might be difficult to classify because ...

#### 2. Discuss the model's predictions on these new traffic signs and compare the results to predicting on the test set. At a minimum, discuss what the predictions were, the accuracy on these new predictions, and compare the accuracy to the accuracy on the test set (OPTIONAL: Discuss the results in more detail as described in the "Stand Out Suggestions" part of the rubric).

Here are the results of the prediction:

| Image			        |     Prediction	        					| 
|:---------------------:|:---------------------------------------------:| 
| Stop Sign      		| Stop sign   									| 
| U-turn     			| U-turn 										|
| Yield					| Yield											|
| 100 km/h	      		| Bumpy Road					 				|
| Slippery Road			| Slippery Road      							|


The model was able to correctly guess 4 of the 5 traffic signs, which gives an accuracy of 80%. This compares favorably to the accuracy on the test set of ...

#### 3. Describe how certain the model is when predicting on each of the five new images by looking at the softmax probabilities for each prediction. Provide the top 5 softmax probabilities for each image along with the sign type of each probability. (OPTIONAL: as described in the "Stand Out Suggestions" part of the rubric, visualizations can also be provided such as bar charts)

The code for making predictions on my final model is located in the 11th cell of the Ipython notebook.

For the first image, the model is relatively sure that this is a stop sign (probability of 0.6), and the image does contain a stop sign. The top five soft max probabilities were

| Probability         	|     Prediction	        					| 
|:---------------------:|:---------------------------------------------:| 
| .60         			| Stop sign   									| 
| .20     				| U-turn 										|
| .05					| Yield											|
| .04	      			| Bumpy Road					 				|
| .01				    | Slippery Road      							|


For the second image ... 

### (Optional) Visualizing the Neural Network (See Step 4 of the Ipython notebook for more details)
#### 1. Discuss the visual output of your trained network's feature maps. What characteristics did the neural network use to make classifications?


