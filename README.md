# About this Project
The Capstone project is the final project for General Assembly Data Science Immersive students, and is a chance to apply their newly developed skills to a subject of their choosing. In my case, I went for image classification, specifically cars becacuse I am a huge car nerd. I first became interested in image recognition after hearing a presentation about [Wayfair's image search feature](https://tech.wayfair.com/tag/image-search/). I feel like there are a lot of applications for this technology, and I find it interesting and challenging to work on. Midway through the project, I also found a [paper](http://www.pnas.org/content/pnas/114/50/13108.full.pdf) that used a similar car recognition models to idenitfy vehicles using Google Streetview.


# Problem Statement
Create a convolutional neural network that classifies images of cars. Ideally, a user should be able to submit a photo of a car, and the model will classify it based on make, model, year, and bodystyle.

# Tools Used
* Keras with tensorflow backend
* AWS for  computing power
* Pandas for data organization

# EDA
* Started with training data images, test data images, and labels dowloaded fom Stanford's website
https://ai.stanford.edu/~jkrause/cars/car_dataset.html

* Initially, images were split into training and test sets of equal size. The class labels for each image were saved in a .mat file, along with bounding boxes for the vehicle in each image.
* I put together a Pandas dataframe called "all_images" containing all images and class info.
* In order to gain more control over the train/test split, I put all the images in the same directory, but renamed them based on whether they were from the original training or test set.
* In order to extract year, make, model, and bodystyle from the class names, I manually edited the dataframe in Excel. This is because of inconsistency in the length of names, as well as missing bodystyle info. For instance, some makes have more than one word (Aston Martin, Land Rover, etc). Where necessary, I added in missing bodystyle information and re-imported the updated dataframe.


# AWS:
*  Due to the computational challenges of neural networks, I used Amazon Web Services in order to improve efficiency.
* I ended up using a compute-optimized GPU. In AWS parlance, it is known as type [p2.xlarge](https://aws.amazon.com/ec2/instance-types/p2/). 
* This was the least expensive GPU designed for computing, and was a sensible choice for my neural net model. In order to run my model, I utilized the tensorflow p36 kernal, which is a Python 3.6 kernal with tensorflow library installed. 
* Performance on AWS was on the order of 10 times faster than using my laptop’s CPU, which was helpful in iterating through epochs an training my model. In order to save money, I did most of my initial data exploration locally, and only used the GPU for model training. 
* One problem I ran into with AWS was uploading large amounts of image files. I solved this by moving them into a .tar (short for Tape Archive) file and uploading all of them in one go.


# Modeling - Image Data Generation
* In order to generate input data for the Convolutional Neural Network, I ran my images through an Image Data Generator. This allowed me to apply labels based on my "all_images" dataframe.
* It also randomly augmented my images through horizontal flips, hortizontal and vertical shifts, shears, and zooms. I decided to convert my images to grayscale because color shouldn't be important in determining vehicle type.

# Modeling - Neural Network
* I used a  Keras Convolutional Neural Network with a tensorflow backend in order to predict classes. In classifying bodystyle (Sedan, Coupe, SUV, Convertible, etc...), I used a network with the following topology and parameters.
* I used 3 convolutional layers, each with 32 filters. The filters were three different sizes: 3x6, 6x3, and 3x3. The purpose of the non-square filters is to detect horizontal and vertical edges.
* For the activation function on most of my layers, I used Leaky Relu. This is a linear function that has a slope of 1 for values greater than zero and a less steep slope for negative values.
* After each layer, I applied some dropout in order to reduce overfitting. I found that a dropout ratio of 0.4 worked well.
* After flattening, which converts the 2d image into an array vector, I applied 2 hidden dense layers of with 18 and 9 nodes, which is proportional to the number of bodystyles I was predicting.
* Finally, I added an output layer of with 9 nodes with softmax activation, which is typical for multicategorical classification.


# Modeling - Training the Neural Network
* I split my data into 60%/40% training and test sets, while stratifying based on bodystyle. In order to avoid going down the wrong path, I trained my model 5 epochs at a time, frequently making changes to the toplogy and parameters when my accuracy score stopped increasing or got worse. 
* I used the categorical cross entropy loss function, which is typical for multicategorical classifaction.
* The training process was very slow and involved hours of trial and error. I made sure to save any models that were promising, and ended up with many different variations.
* Overfitting was a common problem, so I often found myself tweaking dropout or reducing the complexity of my hidden layers.
* Overall I was able to get about 30% accuracy in predicting bodystyle, which is not as good as I would have hoped. There is still much work to be done.

# Areas for Improvement
* I wasn't able to achieve the original vision tha tI had for my project, mostly due to poor results from my neural network. Originially, I wanted to create a classifier that could idenitify the make model, year, and bodystyle of any car.
* The Stanford dataset was supposed to be an initial first step to help me get started with my neural network, but training a convolutional neural  net is a lot harder than expected.
* So far, I have not been able to predict beyond bodystyle. With more time and training, I could probably get more specific.
* I also would like to add some graphs, and visualisations of the filters.
* Looking back, I should have started using AWS much sooner than I did. It saved a lot of time training the model.
