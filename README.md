# Calibration-and-abstention-for-chest-X-ray-pneumonia-classification

A baseline model asking two questions about a pneumonia classifier:
1.	When the model says it is 95% confident, is it right about 95% of the time? 
2.	Does the model make fewer errors if it hands its least confident cases to a doctor? 

Data
Public Kaggle dataset: Chest X-ray Images (labeled-chest-xray-images). It comes with train and test folders and two classes, NORMAL and PNEUMONIA. Test set: 624 images.

Results (test set, 624 images, one run)

| Measure | Value | 
| --- | --- | --- |
| Accuracy| 	93.1% | 
| Sensitivity (pneumonia found)| [SENS]% |
| Specificity (normal found)| 	[SPEC]% | 
| ECE before temperature scaling| 	0.042 | 
| ECE after temperature scaling (T = 0.70) | 	0.050 | 

