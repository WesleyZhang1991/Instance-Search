# [Revisiting instance search: a new benchmark using Cycle Self-Training](https://www.sciencedirect.com/science/article/abs/pii/S0925231222007445)

This code has the source code for the paper "Revisiting instance search: a new benchmark using Cycle Self-Training". Including:

* the annotation description

* the evaluation protocal and leaderboard

* Train/Evaluate/Demo code

## Annotation

### 1. Annotation description
* Please download original images from [CUHK-SYSU](https://github.com/ShuangLI59/person_search)[[2](#refer-anchor-2)] and [PRW](http://zheng-lab.cecs.anu.edu.au/Project/project_prw.html)[[3](#refer-anchor-3)]. Other information including the naming style, image size, etc., can be also found in the original project pages.

* We provide txt files for annotation in folder `annotation`. The annotation protocal is similar to PRW, `gallery.txt` and `query.txt` inlucde annotation with the format as [ID, left, top, right, bottom, image_name]. Only objects that pass through at least 2 cameras are taken into account.
  * The INS-PRW dataset has 7,834 bboxes for `INS_PRW_gallery.txt` and 1,537 bboxes for `INS_PRW_query.txt`.
  * The INS-CUHK-SYSU has 16,780 bboxes for `INS_CUHK_SYSU_gallery.txt` and 6,972 bboxes for `INS-CUHK_SYSU_query.txt`. `INS_CUHK_SYSU_local_gallery.txt` is the local distractor for each query, which is similar to the local gallery setting in original CUHK_SYSU.

### 2. Annotation Visualization
* Sample images from INS-PRW and INS-CUHK-SYSU are shown below. Note the `non saliency(great scale change)`, `class-agnostic`, `great view change` feature of the datasets.

<p align="center">
  <img src="images/Sample.png" width="60%" />
</p>


### 3.Data Statics

* The statics of INS-PRW and INS-CUHK-SYSU are shown below.

| Name             | IDs  | Images | Bboxes | Qeuries |
| :--:             | :--: | :--:   | :--:   | :--:    |
| INS-PRW | 535 | 6,079  | 7,834  | 1,537    |
| INS-CUHK-SYSU | 6,972 | 9,648  | 16,780  | 6,972   |


* Distribution of normalized area and aspect ratio of the annotated boxes. From (a), the boxes occupy much smaller area in the full image compared with the person boxes, which means they are much harder to be localized. From (b), the aspect ratio distribution of the new annotated objects is more spreading out compared to those of person boxes, meaning that our objects are of wider variations.

<p align="center">
  <img src="images/statistics_1.jpg" width="70%" />
</p>

* Category statistics of the two annotated datasets are shown below. There is only a small portion of VOC predefined categories.

<p align="center">
  <img src="images/Category.png" width="70%" />
</p>

## Evaluation
Given a query image with a bounding box, an instance search algorithm should produce multiple prediction boxes (with ranking scores) on the gallery frames. A prediction is regarded as true positive only if it has enough overlap (iou>0.5) with the ground truth bounding box which has the same ID as the query.

* Metrics
    * mAP: For each query, we calculate the area under the Precision-Recall curve, which is known as average precision(AP)[[1](#refer-anchor-1)]. Then the mean value of APs of all queries, i.e. mAP is obtained.
    * CMC: Cumulative Matching Characteristic, which shows the probability that a query object appears in deifferent-sized candidate lists. The calculation process is described in [[1](#refer-anchor-1)].

* Global gallery for INS-PRW: All gallery scene images are used for each query. The setting is identical to [[3](#refer-anchor-3)].

* Local gallery for INS-CUHK-SYSU: The local gallery distractors are added in INS_CUHK_SYSU_local_gallery.txt. Each query search in the local gallery set of 100 scene images (ground truth + local distractors). See [[2](#refer-anchor-2)] for details.

## Leardboard(rank-1/mAP)

We evaluate DELG[[4](#refer-anchor-4)], SiamRPN[[5](#refer-anchor-5)], GlobalTrack[[6](#refer-anchor-6)] and proposed methods in INS-CUHK-SYSU and INS-PRW datasets as follows:

| Method             | INS-CUHK-SYSU | INS-PRW   |
| :--:               | :--:          | :--:      |
| DELG               | 2.0(1.2)      | 0.0(0.0)  |
| SiamRPN            | 16.0(14.2)    | 0.0(0.0)  |
| GlobalTrack        | 28.4(27.8)    | 0.2(0.2)  |
| our baseline       | 43.1(42.1)    | 18.7(8.5) |
| our selftrain      | **49.4(47.4)** | **24.2(13.4)** |

## Code
We use INS-CUHK-SYSU as an example.

### Eval
`sh experiments/baseline_ins.sh` to evaluate datasets.

### Demo
`sh experiments/demo.sh` to run instance search on several sample images.

### Train
For simplicity, we only use 3 images from `images/self_train_samples` as self-train source.
`sh experiments/gen_query_seed.sh` to prepare query seed files.
`sh experiments/self_train.sh` to start cycle self training.


## Citation
```
@article{zhang2022revisiting,
  title={Revisiting instance search: a new benchmark using Cycle Self-Training},
  author={Zhang, Yuqi and Liu, Chong and Chen, Weihua and Xu, Xianzhe and Wang, Fan and Li, Hao and Hu, Shiyu and Zhao, Xin},
  journal={Neurocomputing},
  year={2022},
  publisher={Elsevier}
}
```

## Refenrence
<div id="refer-anchor-1"></div>

[1] [Scalable Person Re-identification: A Benchmark](https://www.cv-foundation.org/openaccess/content_iccv_2015/papers/Zheng_Scalable_Person_Re-Identification_ICCV_2015_paper.pdf)

<div id="refer-anchor-2"></div>

[2] [Joint Detection and Identification Feature Learning for Person Search](https://openaccess.thecvf.com/content_cvpr_2017/papers/Xiao_Joint_Detection_and_CVPR_2017_paper.pdf)

<div id="refer-anchor-3"></div>

[3] [Person Re-identification in the Wild](https://openaccess.thecvf.com/content_cvpr_2017/papers/Zheng_Person_Re-Identification_in_CVPR_2017_paper.pdf)

<div id="refer-anchor-4"></div>

[4] [Unifying deep local and global features for image search](https://arxiv.org/pdf/2001.05027.pdf)

<div id="refer-anchor-5"></div>

[5] [High Performance Visual Tracking with Siamese Region Proposal Network](//openaccess.thecvf.com/content_cvpr_2018/papers/Li_High_Performance_Visual_CVPR_2018_paper.pdf)

<div id="refer-anchor-6"></div>

[6] [GlobalTrack: A Simple and Strong Baseline for Long-term Tracking](https://arxiv.org/pdf/1912.08531.pdf)
