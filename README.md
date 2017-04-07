
# Using Deep Learning to Clone Driving Behavior

## In my previous post on self-driving cars and related Udacity Nano degree project, I was writing about the use of deep neural network for…

## Using Deep Learning to Clone Driving Behavior

In my [previous post](https://medium.com/@bdjukic/recognizing-traffic-signs-with-deep-neural-network-a725cd5a39fe) on self-driving cars and related [Udacity Nano degree ](https://www.udacity.com/drive)project, I was writing about the use of deep neural network for traffic sign classification. This time around, I will be using deep neural network approach in order to mimic human driving behavior (in this particular case, driving a car in a simulator).

## Project goal

The general idea here is to gather training data by driving the car in simulator, then train the deep neural network with that data, and in the end let the car be driven by the model generated by the deep neural network.

![You can find the simulator git repo [here](https://github.com/udacity/self-driving-car-sim)](https://medium2.global.ssl.fastly.net/max/2046/1*hmhb0SevbVf-NlAOIwFp8A.png)*You can find the simulator git repo [here](https://github.com/udacity/self-driving-car-sim)*

## Training data analysis

Udacity has already offered some pre-recorded laps, but I have decided to play around with simulator myself. So I’ve recorded **5 laps **for each direction of the track. Both directions are needed in order to avoid the bias of the deep neural net towards turning to the left side of the road.

The recording resulted in **36 534** captured images. Images contain captured data from three cameras on the car: left, center and right.

![](https://medium2.global.ssl.fastly.net/max/2000/1*4xKnfzutRpIIYYBXxnajIg.jpeg)**

![](https://medium2.global.ssl.fastly.net/max/2000/1*hlENAoKLxoExP1WW5wP42g.jpeg)**

![Captured images from left, center and right camera from the car](https://medium2.global.ssl.fastly.net/max/2000/1*BJbNlnEiZ0AWtzw6ui69oQ.jpeg)*Captured images from left, center and right camera from the car*

For the training purposes I’ve used only center camera which proved to be enough for getting very decent end-results. In order to make the model more general, it is advised to use all three cameras for car to be able to handle scenarios for getting back to the center of the track better.

When using multiple cameras, it is important to remember that steering angle needs to be adjusted properly with a constant for both left and right camera.

![Multiple camera capturing](https://medium2.global.ssl.fastly.net/max/3840/1*DxYHS0sp09mHATC_F1OG8A.png)*Multiple camera capturing*

The training data also contains CSV file which contains time stamped image captures together with steering angle, throttle, break and speed.

![CSV file contains the metadata from the training data](https://medium2.global.ssl.fastly.net/max/3024/1*7yYxPnsiH7GL3-IvVKs1iw.png)*CSV file contains the metadata from the training data*

Since the training data contains laps in both track directions, the steering angle is not biased:

![](https://medium2.global.ssl.fastly.net/max/2492/1*92W6oVVndLdcaPPfubfIwg.png)**

80% of the data is intended to be used for training and 20% for validation. All the training data can be found [here](https://1drv.ms/u/s!As2zxTr-3Rwhg-t_9G7Eh6Nuw3AmCQ).

## Data augmentation and preprocessing

In order to increase the size of the training data set even more, I’ve flipped every image around horizontal axis.

Preprocessing of images in this case is done in the model itself since it’s less expensive when it’s done on GPU compared to CPU. I’ve cropped 50 pixels from top and 20 pixels from the bottom of every image in order to remove unnecessary noise from the images (front part of the car, sky, trees, etc.).

## Model Architecture

I’ve implemented deep neural network architecture from NVidia's paper: [End to end learning for self-driving cars](https://images.nvidia.com/content/tegra/automotive/images/2016/solutions/pdf/end-to-end-dl-using-px.pdf) in [Keras ](https://keras.io/)running [TensorFlow](https://www.tensorflow.org/) in the backend. It's an excellent paper and I recommend going through it if you’re more into understanding the real world application of this deep neural network.

Here’s NVidia’s short video of the car running the same model based on architecture from above in real life:

<iframe src="https://medium.com/media/abfdb22a2477df295dbd26288d4e9b5e" frameborder=0></iframe>

The deep neural network itself takes input images (160 x 320 x 3). At the very beginning it contains normalization and cropping layers. This continues with 3 convolutional layers with 2x2 stride and 5x5 kernel. These are followed by additional 2 convolutional layers with no stride and 3x3 kernel. Convolution layers are connected to three fully connected layers leading to an output control value. Non-linearity is introduced between all network layers with RELU function. ADAM is used for our learning rates.

![Deep neural network architecure used from [NVidia’s paper](https://images.nvidia.com/content/tegra/automotive/images/2016/solutions/pdf/end-to-end-dl-using-px.pdf)](https://medium2.global.ssl.fastly.net/max/2000/1*t-g3enu_7p_KumpEmMmB2Q.png)*Deep neural network architecure used from [NVidia’s paper](https://images.nvidia.com/content/tegra/automotive/images/2016/solutions/pdf/end-to-end-dl-using-px.pdf)*

Dropout layers were not necessary since I have not noticed any significant over-fitting during the training.

## Training and the final outcome

Training was done on Amazon EC2 instance which was equipped with GPUs. After the training was done, the car was able to complete the track by itself:

<iframe src="https://medium.com/media/81c4ad18fdf54c6a1bcb4946035067ba" frameborder=0></iframe>

## Conclusion

All code can be found on my [Github](https://github.com/bdjukic/CarND-Behavioral-Cloning-P3) profile. This has been definitely the most interesting project so far on this course. My plan is to apply the learnings from this project on a real life remote controlled car and build a small racing track in my apartment where I can train it to drive itself.
