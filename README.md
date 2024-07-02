# TML Assignment 3
## Robustness against FGSM and PGD attacks

This assignment trains a robust classifier against FGSM and PGD attacks using available access to a training dataset.

## Table of Contents

- [Data accessible to the adversary](#dataset-accessible-to-adversary)
- [Approach used](#approach-used)
- [Results of implemented approach](#results-implemented-approach)]
- [Alternative approaches used](#alternative-approaches-used)
- [Results of alternative implemented approaches](#result-alternative-approach)

## Data accessible to the adversary
- Partial training data of the victim model.
- Information on the structure of the dataset.
- Attacks done by the adversary on the trained model (FGSM and PGD).

## Approach used
We use the Ensemble Learning technique to obtain better predictive performance against adversarial attacks like FGSM and PGD. Out of the three choices of model architectures provided to us (resnet18, resnet34, and resnet50), we use resnet50 since studies have shown that deeper models tend to be more robust. Following this idea, and combining it with ensemble learning, we create 3 instances of the resnet50 model and initialize them with pre-trained weights. We intend to make the model more and more robust against adversarial attacks, hence the main idea here is that an adversarial example can fool one model but not always the others. Thus, we combine the predictions of all the models and this knowledge is further distilled/transferred into a student model. During the ensemble training, we obtain the ensemble outputs for clean as well as adversarial images generated by applying FGSM and PGD attacks then calculate the distillation loss for all three sets of images (clean, FGSM, and PGD), combining the obtained losses and finally save the trained student model.

This table informs about the hyperparameters and other specifics of our experiments.
| Name                      | Information                        |
|---------------------------|------------------------------------|
| Model used                | ResNet50                           | 
| Pre-trained weights       | ResNet50_Weights.DEFAULT           | 
| Number of ensemble models | 3                                  | 
| Learning Rate             | 0.001                              | 
| Epochs                    | 20                                 | 
| Optimizer                 | SGD                                |
| Epsilon                   | 0.031 (8/255)                      |
| alpha (step size)         | 0.007                              |
| Perturbation steps        | 5                                  |
| alpha (distillation)      | 0.7                                |
| Temperature               | 2.0                                |
| Weight Decay              | 5e-4                               |
| Momentum                  | 0.9                                |

### Results of implemented approach
The above approach results in a clean accuracy of 52.9%, robustness (FGSM) - 45% and robustness (PGD) - 30.1%.

## Alternative approaches used
### Using ensemble learning with all allowed models

### Using TRADES technique
The implemented alternative approach can be accessed in the ____fill this____ file. The main goal of the assignment is to train a classifier that can be robust against the adversarial examples generated by the adversary using FGSM and PGD attacks while keeping into consideration the accuracy-robustness tradeoff. We use the TRADES loss function to obtain a better tradeoff, which minimizes the regularized surrogate loss, like cross entropy loss in our case, for doing the adversarial training. The trade-off regularization parameter beta is introduced in this loss function to control the robustness of the model. Since we intend to train the most robust model, we use a beta value of 6.0 out of the range of 0.0 to 6.0, where 6.0 suggests high robustness. We train the pre-trained resnet50 model for about 100 epochs. We chose resnet50 out of all the allowed models (resnet18, resnet34 and resnet50) since deeper models tend to be more robust. We choose the other hyperparameters by finetuning and referring to the values used in the official implementation of the TRADES loss function.

#### Results of alternative implemented approaches
Clean Accuracy - 59%
Robust Accuracy (FGSM) - 35%
Robust Accuracy (PGD) - 7%

### Adversarial Training for 1 model using FGSM and PGD
We used FGSM and PGD for adversarial training, which resulted in a clean accuracy of less than 50%.



