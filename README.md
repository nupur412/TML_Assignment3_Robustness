# TML Assignment 3
## Robustness against FGSM and PGD attacks

The main goal of the assignment is to train a classifier that can be robust against the adversarial examples generated by the adversary using FGSM and PGD attacks while keeping into consideration the accuracy-robustness tradeoff. 

## Table of Contents

- [Data accessible to the adversary](#dataset-accessible-to-adversary)
- [Approach used](#approach-used)
- [Results of implemented approach](#results-of-implemented-approach)
- [Alternative approaches used](#alternative-approaches-used)
- [Results of alternative implemented approaches](#results-of-alternative-implemented-approaches)
- [Ideas that we could not implement but can improve the current implementation](#ideas-that-we-could-not-implement-but-can-improve-the-current-implementation)

## Data accessible to the adversary
- Partial training data of the victim model.
- Information on the structure of the dataset.
- Attacks done by the adversary on the trained model (FGSM and PGD).

## Approach used
This approach can be accessed in this notebook - [**TML_Assignment3_Main_Approach.ipynb**](https://github.com/nupur412/TML_Assignment3_Robustness/blob/main/TML_Assignment3_Main_Approach.ipynb). The trained model submitted for evaluation can be accessed [here](https://drive.google.com/file/d/1h107hGfkKrnjQsPyGrafp1MRA8i8SQg-/view?usp=sharing).
We use the Ensemble Learning technique to obtain better predictive performance against adversarial attacks like FGSM and PGD. Out of the three choices of model architectures provided to us (resnet18, resnet34, and resnet50), we use resnet50 since studies have shown that deeper models tend to be more robust. Following this idea, and combining it with ensemble learning, we create 4 instances of the resnet50 model and initialize them with pre-trained weights. We intend to make the model more and more robust against adversarial attacks, hence the main idea here is that an adversarial example can fool one model but not always the others. Thus, we combine the predictions of all the models and this knowledge is further distilled/transferred into a student model. During the ensemble training, we obtain the ensemble outputs for clean as well as adversarial images generated by applying FGSM and PGD attacks then calculate the distillation loss for all three sets of images (clean, FGSM, and PGD), combining the obtained losses and finally save the trained student model.

This table informs about the hyperparameters and other specifics of our experiments.

| Name                      | Information                        |
|---------------------------|------------------------------------|
| Model used                | ResNet50                           | 
| Pre-trained weights       | ResNet50_Weights.DEFAULT           | 
| Number of ensemble models | 4                                  | 
| Learning Rate             | 0.001                              | 
| Epochs                    | 10                                 | 
| Optimizer                 | SGD                                |
| Epsilon                   | 0.050                              |
| alpha (step size)         | 0.007                              |
| Perturbation steps        | 5                                  |
| alpha (distillation)      | 0.7                                |
| Temperature               | 2.0                                |
| Weight Decay              | 1e-4                               |
| Momentum                  | 0.9                                |

### Results of implemented approach
For 30% of the samples in the evaluation dataset, the above approach results in a **clean accuracy of 56.1%**
**robustness (FGSM) - 64.5%**
**robustness (PGD) - 31.6%**


**Additional Observation**: 
- While training on just **3 model instances** of Resnet 50, this was the best balance of robustness and accuracy we could obtain for the 12th epoch - **clean accuracy of 55.2%**
**robustness (FGSM) - 59.2%**
**robustness (PGD) - 33.3%**
  The above trained model can be accessed [here](https://drive.google.com/file/d/1RJcids_b2m46dotOVKbg2yn7XIGc1pN_/view?usp=drive_link)
- The model trained for the 15th epoch also showed a robustness accuracy (FGSM) of 69%, but a slightly less robustness accuracy (PGD) of 31.5% for a clean accuracy of 54.9%. This trained model can be accessed [here](https://drive.google.com/file/d/117EE4u0FsG3-KF7L3JHnkeYXUv2jhIwr/view?usp=drive_link)
- We also tried training the model for a large number of epochs but observed that specifically, the accuracy against PGD attack decreased significantly as the number of epochs increased during training

## Alternative approaches used
### Using TRADES technique
The implemented alternative approach can be accessed in the [**TML_Assignment3_Alternative_Approach.ipynb**](https://github.com/nupur412/TML_Assignment3_Robustness/blob/main/TML_Assignment3_Alternative_Approach.ipynb) file. The trained model using this approach can be found [here](https://drive.google.com/file/d/1SwzSApKKvtEp6OeF7v-HboRIA76GXIU0/view?usp=drive_link).
We use the TRADES loss function to obtain a better tradeoff, which minimizes the regularized surrogate loss, like cross entropy loss in our case, for doing the adversarial training. The trade-off regularization parameter beta is introduced in this loss function to control the robustness of the model. Since we intend to train the most robust model, we use a beta value of 6.0 out of the range of 0.0 to 6.0, where 6.0 suggests high robustness. We train the pre-trained resnet50 model for about 100 epochs. We chose resnet50 out of all the allowed models (resnet18, resnet34, and resnet50) since deeper models tend to be more robust. We choose the other hyperparameters by finetuning and referring to the values used in the official implementation of the TRADES loss function.

#### Results of alternative implemented approaches
Clean Accuracy - 59%
Robust Accuracy (FGSM) - 35%
Robust Accuracy (PGD) - 8%

### Using ensemble learning with all allowed models
We implemented a technique similar to our final approach, the only difference was that the ensemble models consisted of resnet18, resnet34 and resnet50 models. Though the performance of this approach was slightly better than other approaches, we found a significant decrease in the robustness accuracy against PGD attack for this approach compared to our main approach. 

### Adversarial Training for 1 model using FGSM and PGD
We used FGSM and PGD for adversarial training, which resulted in a clean accuracy of less than 50%.

## Ideas that we could not implement, but can improve the current implementation
- Applying Transformations to the dataset images could've proved beneficial, as it can help the student model to generalize better. This could've also resulted in a higher robustness accuracy against PGD attack, as transformations can disrupt the gradients, thus improving gradient masking and making it difficult to do a successful PGD attack.
- Using silu activation function instead of ReLU, as silu is shown in studies to work considerably better than others for better adversarial training.


