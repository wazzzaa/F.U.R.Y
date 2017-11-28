## Introduction

The perception of depth from purely 2d stimuli is a classic problem that has captivated the attention of scientists and artists at least since the Renaissance, when Brunelleschi used the mathematical concept of perspective to convey a sense of space in his paintings of Florentine buildings. 

3D human pose can now be estimated reliably by training algorithms to exploit depth data or video sequences. However, estimating such a 3D pose from single ordinary images remains challenging because of the many ambiguities inherent to monocular 3D reconstruction, including occlusions, complex backgrounds, and, more generally, the loss of depth information resulting from the projection from 3D to 2D.

Formally, given an image – a 2-dimensional representation – of a human being, 3d pose estimation is the task of producing a 3-dimensional figure that matches the spatial position of the depicted person. In order to go from an image to a 3d pose, an algorithm has to be invariant to a number of factors, including background scenes, lighting, clothing shape and texture, skin color and image imperfections, among others. Early methods achieved this invariance through features such as silhouettes, shape context , SIFT descriptors or edge direction histograms. While data-hungry deep learning systems currently outperform approaches based on human-engineered features on tasks such as 2d pose estimation (which also require these invariances), the lack of 3d ground truth posture data for images in the wild makes the task of inferring 3d poses directly from colour images challenging.

## Datasets

We used the Human 3.6m dataset to train and test our network. Human3.6M is, to the best of our knowledge, currently the largest publicly available datasets for human 3d pose estimation. The dataset consists of 3.6 million images featuring 7 professional actors performing 15 everyday activities such as walking, eating, sitting, making a phone call and engaging in a discussion. 2d joint locations and 3d ground truth positions are available, as well as projection (camera) parameters and body proportions for all the actors.
Human 3.6M is a dataset which comprises 3.6 million image frames with their corresponding 2D and 3D poses. The subjects perform complex motion scenarios based on typical human activities such as discussion, eating, greeting and walking.

The first task at hand was to obtain a stacked hourglass model as our 2D representation. We leveraged the work of the state of-the-art stacked hourglass network of Newel], pre-trained on the MPII dataset. We use the bounding boxes provided with H3.6M to estimate the centre of the person in the image. We crop a square of size 440 × 440 pixels around this computed centre to the detector (which is then resized to 256 × 256 by stacked hourglass). Stacked Hourglass models are important because they map to probability distributions in 3D space instead of the traditional method of regressing joint heatmaps.

## Data Preprocessing

We have applied standard normalization to the 2d inputs and 3d outputs by subtracting the mean and dividing by the standard deviation. We also centre the 3d poses around the hip joint as is the standard practice while dealing with Human3.6M dataset.

![Fig 1 Architecture: Stacked hourglass network representation of a 2D image is put through our network to obtain a 3D representation of the 2D image](https://github.com/wazzzaa/wazzzaa.github.io/blob/master/arch.JPG)

**Fig 1 Architecture: Stacked hourglass network representation of a 2D image is put through our network to obtain a 3D representation of the 2D image**

## Architecture

Our approach is based on a simple, deep, multilayer neural network with batch normalization , dropout and Rectified Linear Units (RELUs), as well as residual connections. Not depicted in the above image are two extra linear layers: one applied directly to the input, which increases its dimensionality to 1024, and one applied before the final prediction, that produces final output.

### Linear-RELU layers 
Most deep learning approaches to 3d human pose estimation are based on convolutional neural networks, which learn filters that can be applied to entire images. Since we are dealing with low-dimensional points as inputs and outputs, we can use simpler and less computationally expensive linear layers. RELUs are a standard choice to add non-linearities in deep neural networks.

### Residual connections 
Residual connections, have recently been proposed as a technique to facilitate the training of very deep convolutional neural networks , improve generalization performance and reduce training time. The residual connections ease the training of deeper networks. 

### Batch normalization and dropout
Batch normalization and dropout improve the performance of our system while resulting in a slight increase of train and test-time. There is also a constraint on the weights of each layer so that their maximum norm is less than or equal to 1. Coupled with batch normalization, this stabilizes training and improves generalization when the distribution differs between training and test examples. 

## Training 

The implementation is in TensorFlow and we have trained our network for 30 epochs using Adam optimizer, a starting learning rate of 0.001 and exponential decay, using mini-batches of size 64. 

## Experiments

On Human3.6M we follow the standard protocol, using subjects 1, 5, 6, 7, and 8 for training, and subjects 9 and 11 for evaluation. We report the average error in millimetres between the ground truth and our prediction across all joints and cameras, after alignment of the root (central hip) joint. We have trained one model for all the actions part of the Huma3.6M dataset, as opposed to building action-specific models.



![Fig 2: Greeting](https://github.com/wazzzaa/wazzzaa.github.io/blob/master/Greeting.jpg)

**Fig 2: Greeting**


![Fig 3: Smoking](https://github.com/wazzzaa/wazzzaa.github.io/blob/master/Smoking.jpg)

**Fig 3: Smoking**


![Fig 4: Taking a Photo](https://github.com/wazzzaa/wazzzaa.github.io/blob/master/TBD.jpg)

**Fig 4: Taking a photo**

![Fig 5: Walking](https://github.com/wazzzaa/wazzzaa.github.io/blob/master/Walking.jpg)

**Fig 5: Walking**

Figures 2,3,4,5 represent the output of the network on Human actions of Greeting, Smoking, Taking a Photo(selfie you cool kids) and Walking respectively.  The leftmost part represents the 2d observation, the middle part represents the 3d ground truth and the rightmost part represents the 3d predictions we make.

## Concluding remarks and future work
Our network consistently produced a test accuracy of 70% or more over various types of human pose representations that the Human3.6m dataset provides. The simple network provided better  accuracy better than the stacked denoising autoencoder model that we previously planned to incorporate. 

For the future we look to obtain a 3D representation over videos instead of images. Although this is a solved problem, we would love to see how the network performs over videos.


## References
[1] A. Newell, K. Yang, and J. Deng. Stacked hourglass networks for human pose estimation. In ECCV, 2016. 1, 2, 4, 5, 8

[2] K. He, X. Zhang, S. Ren, and J. Sun. Deep residual learning for image recognition. In CVPR, pages 770–778, 2016

[3]TEKIN ET AL.: STRUCTURED PREDICTION OF 3D HUMAN POSE .Structured Prediction of 3D Human Pose with Deep Neural Networks
