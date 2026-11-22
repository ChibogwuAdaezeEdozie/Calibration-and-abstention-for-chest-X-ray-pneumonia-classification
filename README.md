# Calibration-and-abstention-for-chest-X-ray-pneumonia-classification
Calibration and abstention analysis of a ResNet18 chest X-ray pneumonia classifier: does the model know when it is unsure?

Calibration and abstention for chest X-ray pneumonia classification
A baseline model asking two questions about a pneumonia classifier:
1.	When the model says it is 95% confident, is it right about 95% of the time? 
2.	Does the model make fewer errors if it hands its least confident cases to a doctor? 
Data
Public Kaggle dataset: Chest X-ray Images (labeled-chest-xray-images). It comes with train and test folders and two classes, NORMAL and PNEUMONIA. Test set: 624 images.
Method
•	Model: ResNet18 with ImageNet weights, last layer replaced for two classes. Fine-tuned for 5 epochs (Adam, learning rate 1e-4, batch size 32, 224 x 224 images, small random rotation). The loss is class-weighted because the classes are unequal in size.
•	Validation: 15% of the training folder was held out (stratified by class). It was used to pick the best epoch and to fit the temperature. The test folder was used only for the final numbers.
•	Duplicates: exact duplicate images in the training folder were removed using file hashes. No test image was an exact copy of a training image.
•	Calibration: expected calibration error (ECE) with 10 bins, a reliability diagram, and temperature scaling fitted on the validation set.
•	Abstention: test cases sorted by confidence; error rate measured on the most confident share of cases (a risk-coverage curve).
Results (test set, 624 images, one run)
Measure	Value
Accuracy	93.1%
Sensitivity (pneumonia found)	[SENS]%
Specificity (normal found)	[SPEC]%
ECE before temperature scaling	0.042
ECE after temperature scaling (T = 0.70)	0.050
Share of cases answered	Error on answered cases
100%	6.9%
90%	3.6%
80%	1.2%
70%	0.5%
What the results show
•	Abstention helped. Answering only the 80% most confident cases cut the error from 6.9% to 1.2%. The most confident cases (roughly the top 60%) contained no errors. Ranking cases by confidence and deferring the least confident ones is useful on this dataset.
•	Temperature scaling did not help. ECE went from 0.042 to 0.050. The temperature was fitted on the validation set, where accuracy was 98-99%, much higher than on the test set (93.1%). The fitted temperature (0.70) made the model more confident, which did not suit the harder test images. This is consistent with the validation set being too similar to the training set (see limitations).
•	Sensitivity was 100% and specificity 81%.
Limitations
•	The split is by image, not by patient. The file names contain no patient IDs, so the same patient may appear in both training and validation. Validation accuracy (98-99%) was well above test accuracy (93.1%), which is consistent with this. The test numbers are the ones to trust.
•	Results vary between runs. An earlier run of the same code gave 93.9% test accuracy, a fitted temperature of 1.05, and an error of 6.1% when answering every case. Training on a GPU is not exactly repeatable, so differences of about one percentage point should not be over-interpreted. I did not average over several seeds.
•	The reliability diagram is noisy because the low-confidence bins contain very few images.
•	The test set is small, so every percentage above has wide uncertainty. One dataset, no confidence intervals.
•	Only exact duplicates were removed. Near-duplicates are not detected.
•	No external dataset was used, so nothing here says how the model behaves on images from other hospitals or scanners.
Next steps
•	Repeat with a patient-level split on a dataset that provides patient IDs, and fit the temperature on a validation set that is separated from training by patient.
•	Average over several random seeds and report confidence intervals.
•	Test on an external chest X-ray dataset to measure calibration under dataset shift.
•	Compare other uncertainty signals (for example ensembles) with the model's own confidence.

