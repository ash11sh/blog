---
date: "2020-12-07"
draft: false
lastmod: "2020-12-07"
publishdate: "2020-12-07"
tags:
- arts
title: How I trained my Rangoli Art(GAN)
---



<p align="center">
<video width="300" height="300" controls>
  <source src="vid.mp4" type="video/mp4">
</video>
</p>



## Materials  I used :

* Data-set comprising of rangoli images ( ~13k)
* StyleGan2 - Tensorflow
* A little patience 
* Google Colab (Tesla P100  ~24gb ram)



Firstly I thouht of using pre-made data-sets, but decided to build one. Making a data-set is painful process but rewarding. I used instagram and search engine scrappers to download rangoli images. Found many on web, really thankful to the Indian tradition and people. 

<img src="https://live.staticflickr.com/65535/50698038511_e8437cbe77_k_d.jpg" style="zoom:200%;" />

## Image Preprocessing

Downloaded images, now what? 

>  Ans: Trash Unwanted



 Why?

> Ans: Images downloaded may have lot of unrelated items in them.



My cleaning involved these following things:

* removed duplicates 

* hand curated images

* trashed video files, gif files came along

  

## Image Size Conversion

It's a rule that image size must be multiple of 2. (i.e, 64, 256, 512, 1024). So I Converted images to size 256*256 (keeping colab in mind). 

Tool Used for conversion : Image magik (recommended) 



## Model training

Out there many forks are available for Stylegan2, good one is [Peter Baylies](https://github.com/pbaylies/stylegan2) fork.

I used this fork by [woctezuma](https://github.com/woctezuma/steam-stylegan2), which allows you to resume training from where you left.  Converted data into tfrecord format (size matters in colab). You can see generated fake images for every tick (1tick = 5000 imgs). Each takes 15 min time for P100 gpu. 

<img src="https://live.staticflickr.com/65535/50698041696_36464f7d52_k_d.jpg" style="zoom:200%;" />

Calculate the fid metrics (takes about 15min) so that you know how model is performing and when to stop training. 