# Person-reID_GAN
This repository contains the code for our paper [Unlabeled Samples Generated by GAN Improve the Person Re-identification Baseline in vitro](https://arxiv.org/abs/1701.07717).

![](https://github.com/layumi/Person-reID_GAN/blob/master/fig0.jpg)

***News:*** We provide one new end-to-end framework for data generation and representation learning. You are welcomed to check out it at https://github.com/NVlabs/DG-Net

### 1.Unsupervised Learning (GAN)
The first stage is to generate fake images by DCGAN.
We used the code provided in https://github.com/carpedm20/DCGAN-tensorflow and modify some hyper-parameters at https://github.com/layumi/DCGAN-tensorflow. You can directly use my forked code.

For more reference, you can find our modified training code and generating code in `./DCGAN`.
We wrote a detailed [README](https://github.com/layumi/Person-reID_GAN/tree/master/DCGAN). If you still has some question, feel free to contact me (zdzheng12@gmail.com).

### 2.Semi-supervised Learning 
The second stage is to combine the original data and generated data to train the network.
This repos includes the baseline code and the three different methods in the paper.

| Models               | Reference | 
| --------              | -----  | 
| resnet52_market.m        | ResNet50 baseline | 
| resnet52_market_K_1.m    | One extra class for generated images|  
| resnet52_market_lsro.m    | The proposed method, uniform probability | 
| resnet52_market_pseudo.m | Give the most likely label for generated images| 

* You can find more detailed code for proposed loss in 
[[forward code]](https://github.com/layumi/Person-reID_GAN/blob/master/matlab/vl_nnloss.m#L239)
[[backward code]](https://github.com/layumi/Person-reID_GAN/blob/master/matlab/vl_nnloss.m#L291).
(We write the label smooth loss first and then extend it to LSRO. Here we also provide a [brief illustration](https://github.com/layumi/Person-reID_GAN/blob/master/support.pdf).)

* Pseudo label is realized in https://github.com/layumi/Person-reID_GAN/blob/master/matlab/%2Bdagnn/Pseudo_Loss.m


### Compile Matconvnet
**(Note that I have included my Matconvnet in this repo, so you do not need to download it again. I has changed some codes comparing with the original version. For example, one of the difference is in `/matlab/+dagnn/@DagNN/initParams.m`. If one layer has params, I will not initialize it again, especially for pretrained model.)**

You just need to uncomment and modify some lines in `gpu_compile.m` and run it in Matlab. Try it~

(The code does not support cudnn 6.0. You may just turn off the Enablecudnn or try cudnn5.1)

If you fail in compilation, you may refer to http://www.vlfeat.org/matconvnet/install/

### Dataset
Download [Market1501 Dataset](http://www.liangzheng.org/Project/project_reid.html). [[Google]](https://drive.google.com/file/d/0B8-rUzbwVRk0c054eEozWG9COHM/view) [[Baidu]](https://pan.baidu.com/s/1ntIi2Op)
We take Market1501 as an example in this repos and you can easily extend it to other datasets.

### ImageNet Pretrained model
1. Make a dir called `data` by typing `mkdir ./data`.

2. Download [ResNet-50 model](http://www.vlfeat.org/matconvnet/models/imagenet-resnet-50-dag.mat) pretrained on Imagenet. Put it in the `data` dir. 

### Train the Baseline code
1. Add your dataset path into `prepare_data.m` and run it. Make sure the code outputs the right image path.

2. Run `train_id_net_res_market_new.m`. 

### Train with generated data
1. Add your generated data path into `prepare_data_gan.m` and run it. It will add generated image path into the original image database.

2. Run `train_id_net_res_market_K_1.m` for training extra-class method.

Or run `train_id_net_res_market_lsro.m` for training the proposed method.

Or run `train_id_net_res_market_pseudo.m` for training the pseudo-label method.

(What's new: I also include `train_id_net_res_2stream_gan.m` for training the code with the method proposed in my another paper. I do not import all files, and you may find the missing code in https://github.com/layumi/2016_person_re-ID. )

## Test 
1. Run `test/test_gallery_query_crazy.m` to extract the features of images in the gallery and query set. They will store in a .mat file. Then you can use it to do evaluation.
2. Evaluate feature on the Market-1501. Run `evaluation/zzd_evaluation_res_faster.m`.

### Citation
Please cite this paper in your publications if it helps your research:
```
@inproceedings{zheng2017unlabeled,
  title={Unlabeled Samples Generated by GAN Improve the Person Re-identification Baseline in vitro},
  author={Zheng, Zhedong and Zheng, Liang and Yang, Yi},
  booktitle={Proceedings of the IEEE International Conference on Computer Vision},
  year={2017}
}
```

## Related Repos
1. [2stream Person re-ID](https://github.com/layumi/2016_person_re-ID)
2. [Pedestrian Alignment Network](https://github.com/layumi/Pedestrian_Alignment)
3. [MpRL Person re-ID](https://github.com/Huang-3/MpRL-for-person-re-ID)
