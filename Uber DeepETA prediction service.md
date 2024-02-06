# Uber: Accurate ETA Predictions using DeepETA serving Uber's Businesses 

For many years, Uber's Estimated Time of Arrival (ETA) prediction algorithms were based on gradient-boosted decision tree ensembles. As the training data and models grew subsequently with each release, maintaining the model became difficult. Deep Learning techniques began being researched because they allowed for large datasets processing using data-parallel distributed training paradigms. 

XGBoost had already been quite successful at predicting ETA times, and therefore a switch to deep learning techniques would require addressing three main challenges:<br>
1. Latency: The model must maintain low latency of a few milliseconds at most. 
2. Accuracy: The model must register a lower mean absolute error (MAE) over the incumbent XGBoost model.
3. Generality: The model must be able to be used cross-functionally across all of Uber's lines of business like mobility and delivery. 

To deliver on these challenges, Uber AI partnered with Uber's Maps team and developed a deep neural network architecture for global ETA prediction. This article highlights some of the learnings from that [paper](https://arxiv.org/abs/2206.02127?uclick_id=ddaf1042-c35c-460a-9caa-49dd89f2f23f).

## Introduction

The most common method of ETA estimation is with a routing engine, called a route planner. Route planners divide the road networks up into individual road segments, and use shortest-path algorithms to calculate shortest paths between origin and destination. Weighted edges on a graph represent these road segments, and summing these weights over the shortest path gives the ETAs. Traffic patterns, accidents and weather are all considered in modern routing engines. 

The Uber AI approach to ETA prediction uses ML to predict the residuals between routing engine ETA and real-world observed outcomes. 

Based on post-processing machine learning, the model at Uber predicts ETA residuals by considering spatial and temporal features, including origin, destination, request time, real-time traffic, and request nature. Its core objective is to maintain speed to minimize latency in ETA requests while continually improving accuracy measured by MAE across data segments.

Out of the seven different neural network architectures that were tested, Uber found that a shallow encoder-decoder architecture with a linear self-attention yielded the most accurate results. 

![Overview of DeeprETA Post-Processing Architecture from Uber's Blog](Images/uber-deepreta-encoder-decoder-arch.jpg)

Self-attention has been used originally in the context of image processing or natural language processing. For tabular data problems like ETA predictions, the problem statement was defined thus: each feature, such as trip start time of day, origin of trip, is a vector. Self-attention calculates the interaction effects of these K features, stores an intermediate K*K matrix, and outputs the representation of this feature as a weighted sum of all features. In contrast to NLP problems, there is no positional encoding. Multiple attention heads focus one shorter form of each feature's representation across all other features. Thus, the feature <i>speed</i> can be more closely associated with the feature <i>traffic</i> in one head, and with the feature <i>city</i> in another head. 

## Feature Encoding

Continuous features were bucketed in a quantile bucketing strategy, rather than equal bucket sizes, to improve accuracy. Categorical features were embedded using an embedding look-up operation. 

Geospatial features such as origin and destination latitudes and longitudes, were transformed using geohashing and multiple feature hashing techniques. Geohashing refers to mapping each grid cell in a latitude/longitude grid, into a compact range of bins using a hashing function. Multiple feature hashing then is used to map multiple compact ranges of bins for each grid cell, using independent hashing functions. 

![Geohashing using Multiple Feature Hashing from Uber's Blog](Images/uber-deerprETA-multiple-geohash.jpg)

## Two-Layer Module

To keep the model fast and lightweight, a shallow two layer model was chosen. The first layer is a linear transformer, which aims to learn the interactions between geospatial and temporal features, and the second layer is a fully connected layer with calibration, which aims to adjust bias from various request types like delivery/rides or pickup/dropoff.

The request types feature was initially proposed as a feature in the interaction layer. However, due to mean-shift differences across request types, this feature was included as a new layer. 

![DeeprETA Post-Processing Architecture from Uber's DeeprETA paper](Images/uber-deeprETA-arch.jpg)

## Model Training and Inferencing

Various metrics are used for evaluation according to the task. For fare calculations, mean ETA error is important; whereas for evaluating delivery ETA requests, mean ETA error and the 95th quantile are important metrics. Therefore, to meet these diverse requests, a customized metric was determined called asymmetric Huber loss. Asymmetric loss functions apply different weights to underpredictions and overpredictions, when being a minute early may be more expensive than being late. The asymmetric Huber loss contains two parameters, delta and omega, that control the degree of robustness to outliers and the degree of asymmetry respectively. 

Uber uses the Canvas framework to train, retrain and deploy models on to [Michaelangelo, Uber's ML platform](https://www.uber.com/blog/michelangelo-machine-learning-platform/?uclick_id=ddaf1042-c35c-460a-9caa-49dd89f2f23f). 

---
All information in this blog summary is obtained from the [Uber Engineering Blog](https://www.uber.com/blog/deepeta-how-uber-predicts-arrival-times/?uclick_id=ddaf1042-c35c-460a-9caa-49dd89f2f23f)
