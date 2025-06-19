# Test Time Adaptation with MEMO
The repository for the Deep Learning [145903] course project assignment at University of Trento.

In this project I re-implemented [Zhang et al. (2021)](https://arxiv.org/abs/2110.09506) MEMO (Marginal Entropy Minimization with One test point) to improve the performance of the pre-trained model on image classification task at a test time. The purpose of the project is to adapt the model to perform better when the test data distribution differs from the distribution of the dataset it was originally trained on.

### The final report notebook of the Project can be found [HERE](https://github.com/yesunerdene9/deep-learning/blob/main/project_report.ipynb)





(If need to look at the report in github, the visible version is [here](https://github.com/yesunerdene9/deep-learning/blob/main/project_report_cleared_output.ipynb))

## Instructions for running experiments ℹ️

*   If the reader must run the experiment on ***Google Colab***, please set the variable $IS\_COLAB$ to $True$ in the first Preparation cell
    *   The dataset needs to be present in the reader's Google Drive, and please change the image root variables as well
*   If the reader must run the experiment on ***AWS***, please set the variable $IS\_COLAB$ to $False$ in the first Preparation cell
    *   The dataset is uploaded to usergroup-49, and there is no need to change directories; only the notebook needs to be in one directory. If necessary, please adjust the image root variable to suit your environment.
    *   Uncomment lines in the 2nd cell if needed to place the dataset in the desired directory

If anything needs clarification, please send an email to me.

## Discussion about my modification and their results

Due to the large amount of time required to keep the experiment minimal, I limited the augmentation number to 32, although it was ideal to set it to 64, as suggested in the MEMO paper. Nevertheless, my experiment did improve the baseline performance, which was achieved without applying any TTA method. Also for the same reason, I haven't included AugMix as an option to apply as an augmentation; however, one experiment was done with AugMix, but it was taking more than 4 hours, and the result was not competitive with others; therefore, I avoided including AugMix augmentation choice in the modification.

From the results of ResNet50 with weight differences, the new weight V2 has improved performance on both ImageNet-A and ImageNet-V2.

For the Vit.B/16 model, the performance outperforms the baseline, yet it does not surpass that of ResNet50 as a backbone. Unfortunately, none of the other modifications to the model could outperform the model using only RandomResizedCrop as an augmentation method, even though all outperformed the baseline with noticeably higher accuracy.

**Adaptive Batch Normalization**

As the MEMO paper suggested, I inserted the technique into the model that has ResNet50 as a backbone, with the same expectation that the tweaked model's performance would be improved on both datasets; therefore, the subsequent modifications were made all with a BN layer, except for models based on Vit-B/16. 

**Sharpened softmax marginal entropy**

This tweaking approach improved the ResNet-50 (weight V1) as the backbone of the model on ImageNet-A, yielding the best accuracy result **29.87 (+15.6)**.

**Augmentation weighted marginal entropy**

Instead, this modification to the entropy computation approach improved the ResNet-50 (weight V1) as the backbone of the model on ImageNet-A, yielding the best accuracy result **9.17 (+9.14)**. Also, for ImageNet-V2, the modification has given the $2^{\text{nd}}$ best result **68.80 (+6.65)**.


**RandomResizedCrop**

This augmentation choice was used for the previous two modifications as it has given slightly less but relatively stable accuracy results. Additionally, for Vit-B/16 as the backbone of the model in both ImageNet-A and ImageNet-V2, it has yielded the best results, **25.43 (+4.68)** and **72.90 (+3.33)**, respectively.


**RandomResizedCrop + RandomHorizontalFlip**

As the MEMO paper has witnessed the improvement using the horizontal flip standard augmentation method, I implemented it for ResNet50 (weight V2) as the backbone of the model for ImageNet-V2, which gave the best result, **78.90 (+9.01)** accuracy.


**RandomResizedCrop + RandomAffine + RandomPerspective + RandomHorizontalFlip**

As a trial of alternating the augmentation methods, I used RandomResizedCrop, RandomAffine, RandomPerspective, and RandomHorizontalFlip.

## Results

|  Test Result with ResNet50 with weight V1  | ImageNet-A | ImageNet-V2 |
|-----|:------------:|:-------------:|
|                            Baseline                       | 0.03 | 63.15 |
| +MEMO (RandomResizedCrop) | 3.65  | **69.03** |
| +MEMO + BN (RandomResizedCrop) | 8.43  | 68.2 |
| +MEMO + BN (RandomResizedCrop) + Sharpened SoftMax Marginal Entropy | 4.84| 68.03 |
| +MEMO + BN (RandomResizedCrop) + Augmentation Weighted Marginal Entropy | **9.17** | 68.80 |
| +MEMO + BN (RandomResizedCrop + RandomHorizontalFlip) | 8.17 | 67.79 |
| +MEMO + BN (RandomResizedCrop + RandomAffine + RandomPerspective + RandomHorizontalFlip) | 7.51 | 66.13 |



| Test Result with ResNet50 with weight V2| ImageNet-A | ImageNet-V2 |
|-----|:------------:|:-------------:|
| Baseline | 14.27 | 69.89 |
| +MEMO (RandomResizedCrop) | 18.41  | 76.03 |
| +MEMO + BN (RandomResizedCrop) | 24.24  | 78.69 |
| +MEMO + BN (RandomResizedCrop) + Sharpened SoftMax Marginal Entropy | **29.87** | 75.49 |
| +MEMO + BN (RandomResizedCrop) + Augmentation Weighted Marginal Entropy | 24.22 | 76.78 |
| +MEMO + BN (RandomResizedCrop + RandomHorizontalFlip) | 24.31 | **78.90** |
| +MEMO + BN (RandomResizedCrop + RandomAffine + RandomPerspective + RandomHorizontalFlip) | 24.13 | 78.50 |



| Test Result with Vit-B/16 | ImageNet-A | ImageNet-V2|
|-----|:------------:|:------------:|
| Baseline| 20.75 | 69.57 |
| +MEMO (RandomResizedCrop) | **25.43** | **72.90** |
| +MEMO (RandomResizedCrop) + Sharpened SoftMax Marginal Entropy| 23.43 | 72.30 |
| +MEMO (RandomResizedCrop) + Augmentation Weighted Marginal Entropy| 25.17 | 72.82 |
| +MEMO (RandomResizedCrop + RandomHorizontalFlip)| 24.97 | 72.84 |
| +MEMO (RandomResizedCrop + RandomAffine + RandomPerspective + RandomHorizontalFlip) | 24.49 | 72.74 |


