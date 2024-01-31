# Uber: Accurate ETA Predictions using DeepETA serving Uber's Businesses 

For many years, Uber's Estimated Time of Arrival (ETA) prediction algorithms were based on gradient-boosted decision tree ensembles. As the training data and models grew subsequently with each release, maintaining the model became difficult. Deep Learning techniques began being researched because they allowed for large datasets processing using data-parallel distributed training paradigms. 

XGBoost had already been quite successful at predicting ETA times, and therefore a switch to deep learning techniques would require addressing three main challenges:<br>
1. Latency: The model must maintain low latency of a few milliseconds at most. 
2. Accuracy: The model must register a lower mean absolute error (MAE) over the incumbent XGBoost model.
3. Generality: The model must be able to be used cross-functionally across all of Uber's lines of business like mobility and delivery. 

To deliver on these challenges, Uber AI partnered with Uber's Maps team and developed a deep neural network architecture for global ETA prediction. This article highlights some of the learnings from that [paper](https://arxiv.org/abs/2206.02127?uclick_id=ddaf1042-c35c-460a-9caa-49dd89f2f23f).

## Introduction

The most common method of ETA estimation is with a routing engine, called a route planner. Route planners divide the road networks up into individual road segments, and use shortest-path algorithms to calculate shortest paths between origin and destination. Weighted edges on a graph represent these road segments, and summing these weights over the shortest path gives the ETAs. Traffic patterns, accidents and weather are all considered in modern routing engines. 

The Uber AI approach to ETA prediction uses ML to predict the residual between routing enginer ETA and real-world observed outcomes. 

---
All information in this blog summary is obtained from the [Uber Engineering Blog](https://www.uber.com/blog/deepeta-how-uber-predicts-arrival-times/?uclick_id=ddaf1042-c35c-460a-9caa-49dd89f2f23f)
