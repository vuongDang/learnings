# How Differential Privacy Reinforces Privacy of Machine Learning Models?

- [https://link.springer.com/chapter/10.1007/978-3-031-16210-7_54](https://link.springer.com/chapter/10.1007/978-3-031-16210-7_54)

## Context

__Membership Inference Attacks (MIA)__
- Black-box setting, the attacker only has input control and get output from model
- Membership inference vulnerabilities come from model overfitting the training data.
- Attackers try to guess if some data was part of the training data
  - using shadow models of the target model

## Contribution 

1. Comparisons of 3 defense techniques against MIA 
- Standard Regularization: Dropout + L2 Regularization
- Data-Centric Defense: Data Augmentation
- Algorithmic Defense: Differentially Private Stochastic Gradient Descent (DP-SGD)

2. Trade-off analysis for each method 
- Privacy vs utility 

## Key Findings

- __DP-SGD is effective__ 
  - reduce the attack success rate (ASR) close to 50%, like random guessing 
  - this came at the cost of accuracy. For strong privacy the accuracy 
  dropped over 10-15% compared to baseline
- __Data Augmentation brought the best balance__
  - improved both model accuracy while reducing privacy leakage
- __Regularization limits__
  - Dropout and L2 regularization reduced privacy leakage but were less 
  robust against strong adversaries
  
## Limitations 

- Dataset simplicity 
  - evaluation was done on "toy" datasets: MNIST and CIFAR-10
  - accuracy penalty of DP-SGD is known to be more severe on complex tasks:
    - LLMs 
    - high-res medical imaging
- MIA attacks were not adaptive 
  - standard MIA attacks were uses 
  - did not simulate adaptive adversaries 
    - strategize accordingly to the defensive method used

## Overview of the defense techniques evaluated

### Differentially Private Stochastic Gradient Descent (DP-SGD)

#### Gradient Clipping

When some data gradient descent are too high it means that the data influences 
the model weights strongly. 
During training DP-SGD clips gradient descent which are over a specific threshold.
This prevent having single data point having too much impact on the final model.

#### Adding Noise

Once the gradients are clipped, gaussian random noises is added to the sum of the gradients
- this fuzz the data single point but keep the general trend of the model 
- the amount of noise is defined by _sigma_  which is linked to your privacy budget _epsi_

#### Steps

- Collect a batch of L training runs for each gradients 
- Clip gradients that are too high
- Sum the gradients runs batch and add gaussian noise 
  - it's more efficient to add the noise per batch than for each training run individually
- Then divide the gradients by the batch size to get the final gradient update

### Impact

Good: prevent overfitting 
Bad: 
- less precision, needs to find a balance between privacy and precision
- computation overhead
Solution
- use larger training data batches to improve precision while keeping privacy
- require more training time

### Data Augmentation

Create variants of each single point data 
- this increase the size and the diversity of your training data 
- ex: a picture of a cat will have 5 other variants in the training data: crop, flip, rotate, change brightness...
- hence the model learns the concept of a cat rather than the specific pixels of this image 
- the model is less steered strongly towards single data point and "smooth out" the model world representation
- it makes harder for an attacker to perform MIA for a single specific datapoint

### Data Regularization

- __Dropout__
  - during training neurons are randomly shut down temporarily 
  - the model is forced to learn robust patterns and cannot rely on single neurons to remember
  specific secrets 
- __Weight Decay/L2.__ 
  -  the model is punished if its weights is too large 
  - large weights usually indicate that the model is trying to memorize specific outliers
