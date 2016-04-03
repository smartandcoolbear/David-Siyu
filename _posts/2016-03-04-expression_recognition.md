---
title: Facial Expression Recognition 发展现状
layout: post
tags:
  - cnn
  - deep_learning
---

最近几天看了三篇[EmotiW 2015](https://cs.anu.edu.au/few/emotiw2015.html)上的paper,都是关于表情识别的,分别是Image based Static facial Expression Recognition with Multiple Deep Network Learning, Emotion Recognition in the Wild via Convolutional Neural Networks and Mapped Binary Patterns, Hierarchical Committee of Deep CNNs with Exponentially-Weighted Decision fusion for Static facial Expression Recognition.

第一篇用了一个有5个conv层,3个pooling层和3个全连接层,是一个结构比较简单的CNN,用到了一个自己提出的有一定随机性的perturbed方法,用一张图片生成好几张,增加数据,然后先用FER数据集训练CNN,然后再用SFEW数据进行fine-tuning,比较有意思的是fine-tuning的时候只更改全连接层的参数,作者说是为了防止过拟合.作者这样训练了6个模型,然后作者发明了两种方法赋予这6个模型不同权值让它们结合使用能取得比较好的效果,最终在SFEW测试集上预测7种情感达到了61.29%的正确率,并且作者实验得出perturbed方法和多个model结合只是带来了微弱的提升,看来主要提升来自于CNN的设计.对这篇论文有两个地方不明白,作者没有提到用了align的方法,所以输入的图片都是不太"正"的,不知道为什么没有align,还有作者没有明确说那6个模型是怎么训练的,难道只是初始参数不同?

第二篇论文用了很不同的方法,作者是做Face Recognition的大神Hassner,他的创新之处在于使用了LBP特征而不是RGB作为CNN的输入,LBP特征有一个问题是LBP的差值不能反映出两个点真实的差距,作者提出了一种方法对LBP特征进行一次mapping让它的差能反应两个点真实的差距,然后作者用了4个CNN,分别是VGG S,VGG M-2048,VGG M-4096和GoogleNet,然后用5中不同的输入(RGB,LBP及作者对LBP的三种不同处理)一共训练了20个model,训练过程是先用CASIA的训练(不愧是做人脸识别的)然后在用SFEW的数据做fine-tuning,训练数据做了oversampling,先裁出224 * 224的图像,然后分别在四个角和四周多采3个像素变成227 * 227的,然后再取他们的镜像,这样一张图变成了10张,最后对这20个model的权重多次随机赋值,找到效果最好的那一个,最后正确率是54.56%.这篇论文从Face Recognition的角度去做Expression Recognition,提出了用LBP特征做输入的方法,作者实验中10个最好的model中只有一个是RGB作为输入的,看来LBP做输入可能是个不错的idea.这篇论文有两个不明白的地方:一是这篇论文也没有提到align,二是组合model时为什么要随机选权重,有没有更好的方法?最后感觉这篇文章证明了用Face Recognition的特征也可以做Expression Recognition,但他们效果不太好的原因也在于此,他们用了非常复杂的CNN,而现有表情数据库的数据不足以训练这样的model,所以他们用CASIA数据做训练,用SFEW数据做fine-tuning,但SFEW得数据很小,所以最后训练出的model可能还是比较适合做Face Recognition.

第三篇论文是EmotiW 2015的冠军,但它的方法感觉没有太大创新,与第一篇类似,它们先对图片做align,然后设计了三种CNN,由不同的输入,不同的训练数据和不同的初始化训练了216个model,然后用自己提出的方法将这些model组合起来.它们用到了FER和TFD两个external data.感觉它们优于第一篇论文可能是因为用了align和更多的训练数据.但它们用了216个model最后才略胜过第一篇paper,猜测可能是第一篇中CNN设计的更好.

总之看完这三篇论文感觉都是用各种被用烂了的方法拼起来,比如想办法增加训练集,让一张图片生成多张,比如训练多个model结合起来,只有第二篇paper提出了用LBP作为输入的方法比较新颖.