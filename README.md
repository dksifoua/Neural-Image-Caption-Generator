# Neural Image Caption Generator

<p align="center">
  <img src='https://encrypted-tbn0.gstatic.com/images?q=tbn%3AANd9GcSLUUAH8u0JlsGbVVjIJBPojpW34px4JR4_SXPS_mN5QrFZncLD&usqp=CAU'>
</p>

The purpose of image captioning is to automatically generate text describing a picture. In the past few years, it has become a topic of increasing interest in machine learning, and advances in this field have resulted in models (depending on which assessment) can score even higher than humans.


## 1. Requirements

### 1.1. Recommended system

I used free gradient notebook of [paperspace](https://www.paperspace.com/)

- 8 CPUs
- 16 GBs of GPU
- 32 GBs of RAM

### 1.2. Required librairies

```shell
$ pip install -r requirements.txt
$ python -m spacy download en
$ python -m spacy download en_core_web_lg
```

## 2. Data

**Flickr8k dataset** It consists of pairs of images and their corresponding captions (there's five captions fo each image).

Can be downloaded directy using links below:
- [images](https://github.com/jbrownlee/Datasets/releases/download/Flickr8k/Flickr8k_Dataset.zip)
- [captions](https://github.com/jbrownlee/Datasets/releases/download/Flickr8k/Flickr8k_text.zip)

## 3. Model structure

We used the **encoder-decoder architecture** combined with **attention mechanism**. The encoder consists in extracting image representations and the decoder consists in generating image captions.

<p align="center">
  <img src='https://nlpoverview.com/img/image_caption.png'>
</p>

### 3.1. Encoder - Convolutional Neural Network

We fine tuned the pretrained ResNet model without the last two layers.

<p align="center">
  <img src='https://www.researchgate.net/profile/Seunghyoung_Ryu/publication/329954455/figure/fig1/AS:725290594623488@1549934161033/The-structure-of-ResNet-12.png'>
</p>

### 3.2. Decoder - Recurrent Neural Network

<p align="center">
  <img src='https://www.researchgate.net/profile/Savvas_Varsamopoulos/publication/329362532/figure/fig5/AS:699592479870977@1543807253596/Structure-of-the-LSTM-cell-and-equations-that-describe-the-gates-of-an-LSTM-cell.jpg'>
</p>

### 3.3. Attention Mechanism - Badhanau style

As the name suggests, the attention module on each step of the decoder, uses direct connection to the encoder to focus on a particular part of the source image.

<p align="center">
  <img src='https://miro.medium.com/max/1880/0*7YHLBPzQJv_iFKwL.png'>
</p>


## 4. Training

- Number of training parameters: 136,587,749
- Learning rate: 3e-5
- Teacher forcing ratio: 0. This means we don't train using true captions but the previous generated caption.
- Number of epochs: 15
- Batch size: 32
- Loss function: Crossentropy
- Optimizer: RMSProp
- Metrics: Top5 accuracy & BLEU (more below)

## 5. Evaluation - BLEU (Bilingual Evaluation Understudy)

It compares the machine-generated captions to one or several human-written caption(s), and computes a similarity score based on:
- N-gram precision (we use 4-grams here)
- Plus a penalty for too-short system translations

## 6. Inference - Beam Search
While training, we use greedy decoding by taking the argmax. But the problem with this method is that there's no way to undo decision. Instead, we use in inference mode the beam search technique. On each step of decoder, we keep track of the k most probable partial captions (which we call hypotheses); k is the beam size (here 5). Neverthless, using beam search does not guaranteed finding optimal solution.

# References
- Olah, C., & Carter, S. (2016). Attention and Augmented Recurrent Neural Networks.
- Papineni, K., Roukos, S., Ward, T., & Zhu, W. (2002). Bleu: a Method for Automatic Evaluation of Machine Translation. ACL.
- Jay Alammar - Visualizing A Neural Machine Translation Model (Mechanics of Seq2seq Models With Attention) - [link](http://jalammar.github.io/visualizing-neural-machine-translation-mechanics-of-seq2seq-models-with-attention/)