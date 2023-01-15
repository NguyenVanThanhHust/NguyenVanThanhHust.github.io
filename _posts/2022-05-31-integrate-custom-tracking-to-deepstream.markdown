---
layout: post
title: "Integrate custom tracking model to deepstream"
date:   2022-05-31 02:28:15 +0700
categories: [Deepstream, Tracking]
---

# Integrate custom tracking model with Deepstream

## Introduction
Deepstream is a library that allow you to build a pipeline from detection, tracking then visualize result. 

While Deepstream pipeline is straight forward, it is close source and its tracking model is integrated into the library. This would limit our tracking capability. 

In this tutorial, we will learn how to use our own tracking model to Deepstream. I would use their provided docker to demonstrate. But you can use their source code and build it yourself. 

In next tutorial, we will learn how to build our own pipeline without depending on deepstream. 

## Install
![Image on screen](./imgs/test.jpg "Text to show on mouseover")

### Config docker

After install nvidia-docker from [instruction_link](https://cnvrg.io/how-to-setup-docker-and-nvidia-docker-2-0-on-ubuntu-18-04/)

After install 

