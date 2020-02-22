## <font face="楷体">Overview</font>
代码主要参考了[a PyTorch Tutorial to Image Captioning](https://github.com/sgrvinod/a-PyTorch-Tutorial-to-Image-Captioning)
论文地址：[Show, Attend and Tell](https://arxiv.org/abs/1502.03044)

这个Repository主要参考了[sgrvinod](https://github.com/sgrvinod)的[代码](https://github.com/sgrvinod/a-PyTorch-Tutorial-to-Image-Captioning)，实现了soft attention-based Image Captioning的训练，评估，可视化。并且设计了一个简易的[网页版Image Caption Generator](https://github.com/Huntersxsx/Reproduce_Show-attend-and-tell#web-version)，方便使用。

## <font face="楷体">Datasets</font>

- 图像数据集采用的是MSCOCO 14 Dataset，包括[训练集(13GB)](http://images.cocodataset.org/zips/train2014.zip)和[验证集(6GB)](http://images.cocodataset.org/zips/val2014.zip).
- Caption数据集：[caption_datasets](http://cs.stanford.edu/people/karpathy/deepimagesent/caption_datasets.zip)包含了MSCOCO、Flicker8k和Flicker30K三种数据集的JSON file.



## <font face="楷体">Traning</font>

各代码文件的作用可参考[Tutorial/Implementation](https://github.com/sgrvinod/a-PyTorch-Tutorial-to-Image-Captioning#implementation).
具体训练过程可参考[Tutorial/Training](https://github.com/sgrvinod/a-PyTorch-Tutorial-to-Image-Captioning#training).

```
代码中涉及到文件地址的地方请根据文件存放位置自行修改
```

- 首先运行create_input_files.py文件，得到MSCOCO数据集的训练集、验证集和测试集的划分文件，以及WORDMAP文件，保存在output_data/文件夹下:

```
TEST_CAPLENS_coco_5_cap_per_img_5_min_word_freq.json
TEST_CAPTIONS_coco_5_cap_per_img_5_min_word_freq.json
TEST_IMAGES_coco_5_cap_per_img_5_min_word_freq.hdf5
TRAIN_CAPLENS_coco_5_cap_per_img_5_min_word_freq.json
TRAIN_CAPTIONS_coco_5_cap_per_img_5_min_word_freq.json
TRAIN_IMAGES_coco_5_cap_per_img_5_min_word_freq.hdf5
VAL_CAPLENS_coco_5_cap_per_img_5_min_word_freq.json
VAL_CAPTIONS_coco_5_cap_per_img_5_min_word_freq.json
VAL_IMAGES_coco_5_cap_per_img_5_min_word_freq.hdf5
WORDMAP_coco_5_cap_per_img_5_min_word_freq.json
```

- 然后运行train_log.py文件。
    - 起初令fine_tune_encoder = False，checkpoint = None，训练得到没有对Encoder进行微调的模型，当连续8个epoches没有得到提高，则降低learning rate，如果连续20个epoches没有得到提高，则停止训练。最终得到模型：BEST_14_checkpoint_coco_5_cap_per_img_5_min_word_freq.pth.tar
    - 然后接着之前的模型继续进行训练，令fine_tune_encoder = True，checkpoint = ‘BEST_14_checkpoint_coco_5_cap_per_img_5_min_word_freq.pth.tar’，训练得到对Encoder微调后的模型：BEST_20_checkpoint_coco_5_cap_per_img_5_min_word_freq.pth.tar

#### <font face="楷体">Loss and Accuracy</font>

|   | LOSS | TOP5 Acc | Bleu-4 |
| :---: | :---: | :---: | :---: |
| **BEST_14_checkpoint** | 3.219 | 76.371 | 23.404 |
| **BEST_20_checkpoint** | 3.231 | 76.946 | 24.227 |

    
#### <font face="楷体">Model Checkpoints</font>
Pretrained model and WORDMAP ([下载链接](https://pan.baidu.com/s/1eTc6c3wa22RsPsPWRtbuoA)，提取码：ri3n): BEST_14_checkpoint_coco_5_cap_per_img_5_min_word_freq.pth.tar、BEST_20_checkpoint_coco_5_cap_per_img_5_min_word_freq.pth.tar、WORDMAP_coco_5_cap_per_img_5_min_word_freq
有了预训练模型和WORDMAP，就可以对实验结果进行可视化。


## <font face="楷体">Evalution</font>
运行eval.py文件，可以得到不使用Teacher Forcing的结果：

| Beam Size | Val BLEU-4  | Test BLEU-4  |
| :---: | :---: | :---: |
| 1 | 29.30 | 29.48 |
| 3 | 32.12 | 32.24 |
| 5 | 32.25 | 31.78 |

## <font face="楷体">Visualization</font>
运行caption.py文件，要注明imgae、model、WORDMAP的存放路径。
Example:

```
python caption.py --img=F:\PycharmProject\CVNLP\show_attend\visual\img\baby.jpg --model=F:\PycharmProject\CVNLP\show_attend\visual\BEST_checkpoint_coco_5_cap_per_img_5_min_word_freq.pth.tar --word_map=F:\PycharmProject\CVNLP\show_attend\visual\WORDMAP_coco_5_cap_per_img_5_min_word_freq.json --beam_size=5
```
最终可以得到可视化结果：
**BEST_14_checkpoint**：

&lt;start&gt; a baby in a high chair eating food &lt;end&gt;

![](https://github.com/Huntersxsx/Reproduce_Show-attend-and-tell/blob/master/img/Visual1.png)

**BEST_20_checkpoint**：

&lt;start&gt; a baby sitting in a high chair in front of a plate of food &lt;end&gt;

![](https://github.com/Huntersxsx/Reproduce_Show-attend-and-tell/blob/master/img/Visual2.png)


## <font face="楷体">Web Version</font>
利用可视化的结果，我还设计了一个简易的网页版Image Captioning Generator，可以自由选择需要标注的图片，而无需每次更换图片名称在终端重复执行。

```
代码中涉及到文件地址的地方请根据文件存放位置自行修改
```

```
cd Web_IC
python web.py
```

在浏览器中访问 http://127.0.0.1:9250/
会出现网页版的Image Captioning Generator界面：

![](https://github.com/Huntersxsx/Reproduce_Show-attend-and-tell/blob/master/img/Web1.png)

选择文件后，点击Confirm，就会得到结果：

![](https://github.com/Huntersxsx/Reproduce_Show-attend-and-tell/blob/master/img/Web2.png)


