# Joint Part of Speech Embedding (JPoSE)

This repository contains an implementation of the Joint Part of Speech Embedding
model used in Fine Grained Action Retrieval through Multiple Parts-of-Speech
Embeddings[1]. This is a re-implementation of the original paper which used
tensorflow, and now uses Python 3 and PyTorch.

## Features
Features and trained models are coming soon! This will include the empty data
directory allowing for training of the models.

## Quick Start Guide

### Requirements
A conda environment can be installed using `conda env -n create JPoSE_environment.yml`.
This project has been tested with pytorch 1.2 and 1.4.

### Data
You can download the data directory including the dataframes and pickle files
used to train JPoSE _COMING SOON_.

### Training
The models can be trained using the scripts within src/train using the following
command: `python -m train.train_JPoSE_triplet`. (Note that this requires adding
the src/ directory into your python path: `export PYTHONPATH=src/`).

This will train the model using the default values within in 
`/src/defaults/EPIC_JPOSE.py`. The parameters of the model can be passed to the
script, running `python -m train.train_JPoSE_triplet --help` will give the full
list of arguments.

## Differences to [1]
Due to the different implementation, there are a few differences in details, particularly training details.
Namely (values on the right are defaults in this code base):
* Learning rate 1e-5 -> 0.01
* Batch size 256 -> 64
* margin 0.1 -> 1.0
* number of sampled triplets 100 -> 10
* optimiser Adam -> SGD
* num epochs 4000 -> 100 (50 is also suitable for MMEN)
* vv weight 0.1 -> 1.0
* tv weight 1.0 -> 2.0
* tt weight 0.1 -> 1.0
* final embedding size 256 -> 512

Additionally, the final learned layer (f 'hat' and g 'hat' in [1]) is not
included, the features are not used, instead the underlying part of speech
features are simply concatenated.

## Citation
If you use this code, please kindly cite the following:
```
@inproceedings{wray2019fine,
    author    = {Wray, Michael and Larlus, Diane and Csurka, Gabriela and Damen, Dima},
    title     = {Fine-Grained Action Retrieval through Multiple Parts-of-Speech Embeddings},
    booktitle = {ICCV},
    year      = {2019}
}
```
If you train or evaluate on EPIC-Kitchens please kindly cite:

```
@inproceedings{damen2018scaling,
   title={Scaling Egocentric Vision: The EPIC-KITCHENS Dataset},
   author={Damen, Dima and Doughty, Hazel and Farinella, Giovanni Maria  and Fidler, Sanja and 
           Furnari, Antonino and Kazakos, Evangelos and Moltisanti, Davide and Munro, Jonathan 
           and Perrett, Toby and Price, Will and Wray, Michael},
   booktitle={European Conference on Computer Vision (ECCV)},
   year={2018}
} 
```
or
```
@article{damen2020rescaling,
   title={Rescaling Egocentric Vision},
   author={Damen, Dima and Doughty, Hazel and Farinella, Giovanni Maria  and Furnari, Antonino 
           and Ma, Jian and Kazakos, Evangelos and Moltisanti, Davide and Munro, Jonathan 
           and Perrett, Toby and Price, Will and Wray, Michael},
           journal   = {CoRR},
           volume    = {abs/2006.13256},
           year      = {2020},
           ee        = {http://arxiv.org/abs/2006.13256},
}
```
depending on the version of the dataset.

## Modifying the Codebase FAQ

### I want to train on another Dataset
Look in `/src/datasets/mmen_dataset.py` and `/src/datasets/jpose_dataset.py` for
an example on how to do this. A `JPOSE_Dataset` is a wrapper for three 
`MMEN_Datasets` which correspond to Verb, Noun and Actions.

The input files required are as follows for each `MMEN_Dataset`:
* word embedding features, i.e. from Word2Vec[2]
* video features, from any pre-trained model
* relational dictionaries. These relate videos to classes and sentences to
  classes and vice versa, note that this is because of the many-to-many 
  relationship of EPIC-Kitchens. See below for creating these.
* relevance matrix. This contains an NxM matrix which gives the relevance
  between videos (N: number of videos) and captions (M: number of captions).
  For example, the ith, jth element in this matrix is the similarity between the
  ith video and the jth caption. See below for creating these.

### I want to train the baseline model in [1]
The MMEN model can be trained using the comand `python -m
train.train_mmen_triplet` in a similar way to JPoSE.

### How do I create the relational dictionaries?
The relational dictionaries are a set of four dictionaries, vid2class,
class2vid, sent2class and class2sent. Each one is a python dictionary with the
value being a list of relevant videos, sentences or classes depending on the
dictionary. The dictionaries are used during training to sample triplets. These
can be created using the command `python scripts.create_relational_files
DATAFRAME_NAME` and only require being made for the training set.

### How do I create the relevance matrix?
The relevance matrix is an NxM matrix which gives the relevance between videos
(N: number of videos) and captions (M: number of captions).  For example, the
ith, jth element in this matrix is the similarity between the ith video and the
jth caption. This can be creating using the command `python
scripts.create_relevancy_files.py DATAFRAME_NAME` and only require being made
for the test set.

## Copyright
Authors and Department of Computer Science. University of Bristol.

Code is published under the Creative Commons Attribution-NonCommercial 4.0
International License. This means that you must give appropriate credit,
provide a link to the license, and indicate if changes were made. You may do so
in any reasonable manner, but not in any way that suggests the licensor
endorses you or your use. You may not use the material for commercial purposes.

## Disclaimer
We make no claims about the stability or usability of the code provided in this
repository.

We provide no warranty of any kind, and accept no liability for damages of any
kind that result from the use of this code.

## References
[1] Wray et al, Fine-Grained Action Retrieval through Multiple Parts-of-Speech
Embeddings, ICCV 2019
[2] Mikolov et al, Efficient estimation of word representations in vector space, ArXiv 2013.
