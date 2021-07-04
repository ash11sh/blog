---
date: "2020-11-11"
draft: false
lastmod: "2020-11-11"
publishdate: "2020-11-11"
tags:
- CV
title: Building Image Dataset
---

It's bit of hectic process in creating image datasets. It Basically consists of below mentioned pipeline.(to my understanding)

1. Model Bias.
2. Whats your model goal?
3. Ways to collect images.
4. Cleaning the data.
5. Resizing the images.

-----


### Model Bias

 Can you solve this riddle??

 >A man and his son are in a terrible accident and are rushed to the hospital in critical care. The doctor looks at the boy and exclaims "I can't operate on this boy, he's my son!" How could this be?


Firstly most people generally think what i think😃, this is an example for human bias.

If you train your model with more cat images and expect it to perform well on detecting cats and dogs, this happens

{{< figureCupper
img="dog.jpg" 
caption=" [Credits: Sidney Harris](http://www.sciencecartoonsplus.com/index.php)." 
command="Resize" 
options="700x" >}}


For more details on data bias you can go through this excellent slides by cs224n: [Bias in the Vision and Language of Artificial Intelligence](https://web.stanford.edu/class/archive/cs/cs224n/cs224n.1194/slides/cs224n-2019-lecture19-bias.pdf)



### Ways to collect data
here's a just a sample list of sources to collect images data

* Search engines 🔍
(Google, Bing, Yandex, Duck Duck Go)
* Social Media > through hashtags#️⃣
* Youtube videos and flickr📹
* take a camera/mobile and go around collect data by yourself.

### Cleaning the data. 
1. Trash the Images which can't be loaded/ corrupted.
2. find out duplicate images(due to various search engines).
3. Do what's necessary...

### Resizing the images

* Resize maintaining its aspect ratio. 
* If you have images of different sizes, and you try using resize with padding(filling the pixels with black/white).
* Smaller your images >>> faster your model training.


----


### Codes you need(💪 open source)

> Some of these requires chromedriver and selenium.

For images downloading based on Search engines:
* Image Downloader by "[sczhengyabin](https://github.com/sczhengyabin/Image-Downloader)"  [google | bing]

* google images download by [hardikvasa](https://github.com/hardikvasa/google-images-download)

* yandex images download by [bobokvsky](https://github.com/bobokvsky/yandex-images-download)

* Bulk Bing Image downloader by [ostrolucky](https://github.com/ostrolucky/Bulk-Bing-Image-downloader)

* Flickr image-scraping software developed by [Ultralytics LLC](https://github.com/ultralytics/flickr_scraper)


For downloading from instagram based on hashtags:
* Instagram-scraper by [arc298](https://github.com/arc298/instagram-scraper)



For duplicate images cleaning:
* Imagededup by [idealo](https://github.com/idealo/imagededup)✨ 😎

This "imagededup" package uses Convolutional Neural Network (CNN) and hashing algorithms to find duplicates in images.