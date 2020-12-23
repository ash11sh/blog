

---
date: "2020-12-23"
draft: false
lastmod: "2020-12-23"
publishdate: "2020-12-23"
tags:
- CV
title: Yolov5 training with Custom Dataset
---







<p><center><img src="https://live.staticflickr.com/65535/50750405757_115792ed54_o_d.jpg" alt="joey" style="zoom:130%;" /></center></p>



## intro

I recently came across wildlife projects using AI and how they are helping in protecting endangered species and thought of making a model to help detecting tigers🐅 in the wild. Scroll down, too see how i trained my object detection model.

​												

If you are active in computer vision, you may have heard about [yolov5](https://github.com/ultralytics/yolov5).  There's some controversy around its naming, you can read details from [here.](https://medium.com/swlh/yolov5-controversy-is-yolov5-real-20e048bebb08) Ultralytics team put a great effort in open-sourcing this model 👏👏👏

​								
<p><center><img src="https://live.staticflickr.com/65535/50750867037_e62c653d0c_d.jpg" alt="yolo" style="zoom:130%;" /></center></p>


I decided to use yolov5s model, which is light weight version and claiming better fps on edge devices.



## data

Regarding data, I googled about `tiger dataset` and got to know about *Amur Tiger Re-identification in the Wild* (ATRW) dataset.  

> data-set website: https://cvwc2019.github.io/challenge.html 
>
> paper: https://arxiv.org/abs/1906.05586

Train set and Validation set consists of 2485 and 277 images respectively. And converted the data-set which is labelled in voc (.xml) format to yolo (.txt) format using this code: [link](https://github.com/bjornstenger/xml2yolo)

``If your dataset had unlabelled images, use tools like CVAT, makesense.ai or labelme to annotate them ``



data directory looks like this:

```markdown
├── train
│   ├── images
│   └── labels
└── valid
    ├── images
    └── labels
```



Prepare `data.yaml ` file, before proceed to training like this:

```yaml
train: ../train/images
val: ../valid/images

nc: 1
names: ['tiger']
```

here `nc  `  refers to number of classes.



## train

Zip the entire folder along with yaml file and uploaded to google drive, so that easy to download in colab. Based on your luck and timing you may get P100 gpu in google colab, use it to train the model.



* Clone repo and Install required dependecies:

```sh
$ !git clone https://github.com/ultralytics/yolov5  # clone repo
$ %cd yolov5
$ !pip install -r requirements.txt  # install dependencies
```



* download the dataset from gdrive and unzip it.

  ```sh
  $ !gdown --id <your dataset download-id> ##download from drive
  $ !unzip data.zip # unzip your dataset
  ```

> go through this [link](https://github.com/wkentaro/gdown) to get familiar with **gdown**



* start training by selecting input image size, batch size and setting number of epochs

  ```shell
  $ !python train.py --img 640 --batch 16 --epochs 300 --data data.yaml --weights yolov5s.pt
  ```

> All training results are saved to `runs/train/ directory`



You can go through this [colab notebook](https://colab.research.google.com/github/ultralytics/yolov5/blob/master/tutorial.ipynb) for more info.



## Model Performance Metrics

You can setup [weights and bias](https://wandb.ai/) account and start seeing model metrics and visualise batch images while it is training. It is integrated with yolov5, so that its easy for you to setup.

 {{< note >}}run this step before you start training{{< /note >}}

```shell
# Weights & Biases (optional)
$ %pip install -q wandb  
$ !wandb login
```



> ​																**batch images**
<p><center><img src="https://live.staticflickr.com/65535/50750866972_bd03dd55dd_d.jpg" alt="wb1" style="zoom:130%;" /></center></p>




> ​																**metrics**
<p><center><img src="https://live.staticflickr.com/65535/50750022523_65fb7e8f46_d.jpg" alt="wb2" style="zoom:130%;" /></center></p>



## Onnx model conversion

Install onnx tools 

```shell 
$ !pip install onnx>=1.7.0  # for ONNX export
```



Export model to onnx

```shell
$ !python models/export.py --weights yolov5s.pt --img 640 --batch 1  # export at 640x640 with batch size 1
```

* you can see more about model export on this thread [:link:](https://github.com/ultralytics/yolov5/issues/251)
* About **pytorch** to **tensorflow** model conversion [:link:](https://github.com/ultralytics/yolov5/pull/1127)



## inference

for inference on batch of images:

```shell
!python detect.py --source data/images --weights yolov5s.pt --conf 0.25
```

for video inference:

```shell
!python detect.py --source file.mp4 --weights yolov5s.pt --conf 0.25
```

{{< warning >}}Don't forget to save the model in google drive{{< /warning >}}





<p><center><img src="https://live.staticflickr.com/65535/50750866877_6fdf730351_d.jpg" alt="tiger" style="zoom:130%;" /></center></p>



## final thoughts

Successfully trained the model, now looking forward to use it on edge device like raspberry pi. Last november PyTorch [announced](https://pytorch.org/blog/prototype-features-now-available-apis-for-hardware-accelerated-mobile-and-arm64-builds/) their officials builds for Arm64 devices. (nightly)



{{< expandable label="Installing PyTorch on raspberry(arm64):" level="1" >}}
```shell
$ pip install numpy
$ pip install --pre torch torchvision torchaudio -f https://download.pytorch.org/whl/nightly/cpu/torch_nightly.html
```
{{< /expandable >}}

