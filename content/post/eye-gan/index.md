

---
date: "2021-01-17"
draft: false
lastmod: "2021-01-17"
publishdate: "2021-01-17"
tags:

- CV
title:  Removing Eye-glasses from face

---


## intro
I recently tried to implement code for trying out glass frames like snapchat filter. Used OpenCV and numpy for this process of overlaying frame. Result came out good for frontal posed face.

{{< figureCupper
img="imq.png" 
caption="demo - overlaying frame" 
command="Resize" 
options="500x" >}}

But you need to give input image without wearing glasses to get better results. I tried giving an image of person with wearing glasses and it came out like this.

{{< figureCupper
img="im2.png" 
caption="Warner Bros owns the copyright of Harry Potter films" 
command="Resize" 
options="500x" >}}

We can use *in-painting* methods for deleting eye glasses from image (pixel level editing). It is manual process and replacing pixels with right ones is difficult task. I went through internet for finding any way to removing eye glasses. 

I found a paper from early 2004 [Automatic Eyeglasses Removal from Face Images ](http://people.csail.mit.edu/~celiu/pdfs/eyeglasses-TPAMI.pdf), it uses Markov-chain Monte Carlo method for locating eye-glasses and some complex methods to synthesise face without eye-glasses. Below is the image depicting its process flow.

{{< figureCupper
img="screen.png" 
caption="source:  [Automatic Eyeglasses Removal from Face Images ](http://people.csail.mit.edu/~celiu/pdfs/eyeglasses-TPAMI.pdf)" 
command="Resize" 
options="500x" >}}

​									



Its hard for me to replicate this paper as I don't know much about this complex algorithms. So for keeping it simple task, i went with deep learning methods. Like many people, I too used 'faceapp' for trying out cool features of age conversion, gender transformation etc.,

Faceapp used image to image translation (cyclegan, pix2pix) models for those tasks. It means learning features from one image and applying to another image - **Image Reconstruction**. I decided to give it a shot for reconstructing face without glasses.

{{< figureCupper
img="horse2zebra.jpg" 
caption="*Applying styles between horse and zebra using cyclegan*" 
command="Resize" 
options="500x" >}}

​														

## data set

Okay now here comes data part. For every deep learning application data is crucial and especially for this task. For pix2pix model, we need paired set of data i.e., `same person with glass and without glass` . And for cyclegan model, we can train with unpaired  set of data  i.e., `person X with glass and person Y without glass`.

I prepared data using aligned [*celeb A*](http://mmlab.ie.cuhk.edu.hk/projects/CelebA.html) data-set with approx 200K images. Separated data-set into two categories one with glass and another without glass using *attribute label* file provided with data-set.







## training

* [pytorch-CycleGAN-and-pix2pix](https://github.com/junyanz/pytorch-CycleGAN-and-pix2pix): Used code from this repository for training and testing purpose.

* There's also a TensorFlow version available : [TensorFlow Core CycleGAN](https://github.com/tensorflow/docs/blob/master/site/en/tutorials/generative/cyclegan.ipynb)

* Thankful to google for providing colab platform, which helped with training the model for long hours.

* As I'm trying out cyclegan for first time, i kept the default configuratiion untouched, and changed image loading size according to dataset.

* After training the model for 11 epochs , 2 million+ iterations , with batch size 1, I stopped the process.

* To know how well your model trained, you need to calculate Frechet Inception Distance score (FID). To know more about FID search the internet, you may find well written answers.

* I didn't calculated the FID score, but stopped training the model by seeing the sample outcomes during training process.

  

  

## results

I tested the model, It worked well for some images, and didn't for few others. I inferred above mentioned image into the model and the result is satisfactory for now. 


{{< figureCupper
img="im.png" 
caption="resulted image" 
command="Resize" 
options="500x" >}}

The model not entirely removes the glass but make less visible. May be changing network architectures or changing training procedure will improve it, I think so. 

## conclusion

You can see some what distorted image outcome, and the glass is less visible, not entirely removed. This removal of eye glasses can be applied into different applications like improving face recognition and  for virtual makeup.

Cycle-gan model can be run on CPU but it is memory intensive process, not suitable for low-end devices. My method is somewhat newbie style, I know it is need to be improved in many ways. And I welcome suggestions for improving the model.