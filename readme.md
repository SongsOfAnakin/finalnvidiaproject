# Trash Image Classification

This project is a trash image classification system using NVIDIA Jetson Inference Library. The model has been retrained on a comprehensive trash dataset to classify different types of trash with high accuracy.

## Overview

The system can classify trash into 10 different categories, making it useful for people to organize trash into different places easier.

## The Algorithm

### Model Architecture

[The model](https://drive.google.com/file/d/1rxGChiVVU55-F3HUiedWlTLn6W6AyduL/view?usp=sharing) uses a ResNet-18 architecture that has been retrained on [this trash dataset](https://www.kaggle.com/datasets/sudipp/garbage-dataset-9-classes). For training, 80% of every class was used for training, 15% of every class was used for validation, and 5% was used for testing.

### Trash Categories

The model classifies trash into 10 categories:

1. Trash
2. Shoes
3. Plastic
4. Paper
5. Metal
6. Glass
7. Clothes
8. Cardboard
9. Biological
10. Battery

## Setup

### 1. Install Jetson Inference

```
git clone --recursive https://github.com/dusty-nv/jetson-inference
cd jetson-inference
mkdir build
cd build
cmake ../
make
sudo make install
```

### 2. Prepare Dataset

Organize images like this:
```
jetson-inference/python/training/classification/content/drive/MyDrive/archive/garbage_classification
├── train/
│   ├── trash/
│   ├── shoes/
│   ├── plastic/
│   ├── paper/
│   ├── metal/
│   ├── glass/
│   ├── clothes/
│   ├── cardboard/
│   ├── biological/
│   ├── battery/
├── val/
└── test/

```

### 3. Training

1. Enable more memory: `echo 1 | sudo tee /proc/sys/vm/overcommit_memory`
2. Train the model 
  ```
  cd jetson-inference
  ./docker/run.sh
  cd python/training/classification
  python3 train.py --model-dir=models/garbage_classification data/garbage_classification
  ```
3. Export Model
  ```
  # Still in docker container:
  python3 onnx_export.py --model-dir=models/garbage_classification
  ```

## Using the Model

### Set Variables
```
cd jetson-inference/python/training/classification
NET=models/garbage_classification
DATASET=data/garbage_classification
```

### Test on Image
```
imagenet.py --model=$NET/resnet18.onnx --input_blob=input_0 --output_blob=output_0 --labels=$DATASET/labels.txt $DATASET/test/battery/<image.jpg> result.jpg
```
Replace <image.jpg> with your actual image.
### Live Camera
```
imagenet.py --model=$NET/resnet18.onnx --input_blob=input_0 --output_blob=output_0 --labels=$DATASET/labels.txt /dev/video0 output.mp4
```

### Process Video
```
imagenet.py --model=$NET/resnet18.onnx --input_blob=input_0 --output_blob=output_0 --labels=$DATASET/labels.txt input.mp4 output.mp4
```

## Resources
* [The Finished Model](https://drive.google.com/file/d/17DK6-KhtUVf4gmr7ruBwID5_nUo6QiR5/view?usp=drive_link)
* [Dataset](https://www.kaggle.com/datasets/sudipp/garbage-dataset-9-classes)
* [ImageNet Documentation](https://github.com/dusty-nv/jetson-inference/blob/master/docs/imagenet-console-2.md)
* [Jetson Inference GitHub](https://github.com/dusty-nv/jetson-inference)
* [Video Demonstration](https://drive.google.com/file/d/11awnhadxlp6punGvkqY1aqFNmrTVrS0P/view?usp=drive_link)
