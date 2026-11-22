# Calibration-and-abstention-for-chest-X-ray-pneumonia-classification

A baseline model asking two questions about a pneumonia classifier:
1.	When the model says it is 95% confident, is it right about 95% of the time? 
2.	Does the model make fewer errors if it hands its least confident cases to a doctor? 

**Data**
Public Kaggle dataset: Chest X-ray Images (labeled-chest-xray-images). https://www.kaggle.com/datasets/tolgadincer/labeled-chest-xray-images

**Method**
1. Model: ResNet18 with ImageNet weights, last layer replaced for two classes. Fine-tuned for 5 epochs (Adam, learning rate 1e-4, batch size 32, 224 x 224 images, small random rotation). The loss is class-weighted because the classes are unequal in size.
2. Validation: 15% of the training folder was held out (stratified by class). It was used to pick the best epoch and to fit the temperature. The test folder was used only for the final numbers.
3. Duplicates: exact duplicate images in the training folder were removed using file hashes. No test image was an exact copy of a training image.
4. Calibration: expected calibration error (ECE) with 10 bins, a reliability diagram, and temperature scaling fitted on the validation set.
5. Abstention: test cases sorted by confidence; error rate measured on the most confident share of cases (a risk-coverage curve).

**Results (test set, 624 images, one run)**

| Measure  | Value |
| ------------- | ------------- |
| Accuracy  | 	93.1%  |
| Sensitivity (pneumonia found)  | 100%  |
| Specificity (normal found)  | 	81.6%  |
| ECE before temperature scaling  | 	0.042  |
| ECE after temperature scaling (T = 0.70)  | 	0.050  |

| Share of cases answered  | Error on answered cases |
| ------------- | ------------- |
| 100%  | 	6.9%  |
| 90%  | 3.6%  |
| 80%  | 	1.2%  |
| 70%  | 	0.5%  |

<img width="800" height="356" alt="image" src="https://github.com/user-attachments/assets/940fd56f-98a7-433c-8e93-7bdc6ab7ab65" />

**Intepretation**
1. Abstention helped. Answering only the 80% most confident cases cut the error from 6.9% to 1.2%. The most confident cases (roughly the top 60%) contained no errors. Ranking cases by confidence and deferring the least confident ones is useful on this dataset.
2. Temperature scaling did not help. ECE went from 0.042 to 0.050. The temperature was fitted on the validation set, where accuracy was 98-99%, much higher than on the test set (93.1%). The fitted temperature (0.70) made the model more confident, which did not suit the harder test images. This is consistent with the validation set being too similar to the training set (see limitations). In an earlier run, temperature scaling changed ECE from 0.048 to 0.044, so across the two runs it made no consistent difference.
3. Errors were all false alarms. Sensitivity was 100% (no pneumonia case was missed) and specificity was 81.6%, so every error was a normal X-ray flagged as pneumonia. That is the safer direction for a screening tool, but it means nearly 1 in 5 normal X-rays would be sent for review. 38 of 624 images had confidence below 90%.
