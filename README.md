# Object Detection in an Urban Environment

## Project overview

For this project, we are trying to train a neural network that can efficiently detect objects in the waymo dataset.

Self-dering car system relies on object detection to do situation awareness which helps the car understand the environment.

In this project, we only have a limited amount of datasets.

First, we need to analyze the dataset and try to augment the dataset.

Then choosing a better model to retrain will help to increase both training time and accuracy of the final result.

## Set up

Build the image with:
```
docker build -t project-dev -f build/Dockerfile .
```

Create a container with:
```
docker run --gpus all -p 8080:8080 -p 6006:6006 --shm-size="16g" -v .:/home/workspace -ti project-dev bash
```

Download the data from workspace to project root directory

**Note**: I do downgrade some lib to avoid some runtime issues. This dockerfile is runnable in windows 10 with docker wsl2 and cuda11.7

## Dataset

### Dataset Analysis

All the images come from the front camera in a vehicle. So the camera position is stable.
All images should only contain three types of labeled objects which are vehicle, pedestrian, and cyclist.
The image may vary with different light, weather, and time.
In digital image processing, the image may vary with different brightness, contrast, hue, saturation, and noise.
Also, the image resolution is 640x640 which is low resolution in my opinion. Because some bounding box is way smaller than others, some objects will be hard to detect or have a low score of detection.
![alt text](https://github.com/Jackzhang8659/nd013-c1-vision-starter/blob/main/EDA%20Image/segment-10061305430875486848_1080_000_1100_000_with_camera_labels_150.png?raw=true)
![alt text](https://github.com/Jackzhang8659/nd013-c1-vision-starter/blob/main/EDA%20Image/segment-11219370372259322863_5320_000_5340_000_with_camera_labels_10.png?raw=true)
![alt text](https://github.com/Jackzhang8659/nd013-c1-vision-starter/blob/main/EDA%20Image/segment-11355519273066561009_5323_000_5343_000_with_camera_labels_90.png?raw=true)

### Cross-validation

I download the data from workspace which is already split so I did not perform cross-validation strategy. By default, 10% of the data use as validation.

## Training

### Reference experiment

Reference experiment is running with a SSD ResNet50 V1 FPN 640x640 (RetinaNet50) from [model zoo](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf2_detection_zoo.md)

![alt text](https://github.com/Jackzhang8659/nd013-c1-vision-starter/blob/main/output/Tensorboard1.PNG?raw=true)

The algorithm does not perform consistently. The loss is too high and localization loss is not descent. The result is also not good with 0.0002 mAP.

### Improve on the reference

To improve performance, my first idea is to augment my data. Because the dataset is too small, the model needs more data to improve precision. After some testing, I found that random flip of the image and random scale of the image is suitable for augmentation.
Also, I add some camera settings to randomly adjust brightness, contrast, hue, and saturation to mimic the different environments.

Second, I change the model to EfficientDet D1 640x640 which has a higher COCO mAP than the reference one.
However, after running with the default optimizer, the loss quickly descent to 0. I realized that the default momentum_optimizer with cosine_decay_learning_rate and warmup_learning_rate makes the learning rate way too high. So I change to adam_optimizer with manual_step_learning_rate.

![alt text](https://github.com/Jackzhang8659/nd013-c1-vision-starter/blob/main/output/Tensorboard2.PNG?raw=true)

With 15k steps, the model reaches 0.1227 mAP.

**Note** The new pipeline file is under solution folder.

## Result

Comapare the model direct from model zoo and our model 
![image](https://github.com/Jackzhang8659/nd013-c1-vision-starter/blob/main/output/Before2.gif?raw=true)![image](https://github.com/Jackzhang8659/nd013-c1-vision-starter/blob/main/output/After2.gif?raw=true)
