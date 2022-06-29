# Object Detection in an Urban Environment

## Project overview

For this project, we are trying to train a nerual network which can efficiently detect objects in waymo dataset.

Self-dering car system reley on object detection to do situation awareness which help the car understand the environment.

In this project, we need to augumente an exist model with limited dataset to perform a good vehicle, pedestrian and cyclist detection.

With limited amount of datasets, first we need to analysis the dataset and try to augmente the dataset.

Then choosing a better model to retrain will help to increase both training time and accuracy of final result.

Finally, we can compare the result of augmented model to the old model.

## Set up

Build the image with:
```
docker build -t project-dev -f build/Dockerfile .
```

Create a container with:
```
docker run --gpus all -p 8080:8080 -p 6006:6006 --shm-size="16g" -v .:/home/workspace -ti project-dev bash
```

Download the data from workspace to project root dictory

**Note**: I do downgrade some lib to avoid some runtime issue. This dockerfile is runable in windows 10 with docker wsl2 and cuda11.7

## Dataset

### Dataset Analysis

All the images are come from front camera in a vehicle. So the camera postion is stable.
All images should only contain three types of labled object which are vehicle, pedestrian, cyclist.
The image may variinat with different light, weather and time.
In digital processing side, the image may varriant with different brightness, contrast, hue, saturation and noise.
Also, the image resulution is 640x640 which is low resolution in my opinion. Because some bounding box is way more smaller than others, some objects will hard to detect or have a low score of dectection.
![alt text](https://github.com/[username]/[reponame]/blob/[branch]/image.jpg?raw=true)![alt text](https://github.com/[username]/[reponame]/blob/[branch]/image.jpg?raw=true)

## Training

### Reference experiment

### Improve on the reference