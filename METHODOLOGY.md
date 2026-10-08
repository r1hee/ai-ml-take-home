# Methodology

Replace this template with your own. Keep it short: write what a teammate would need to run your work and trust it.

## 1. How to run it
1. Set up the environment 
`
pyt
1. Open task.ipynb
2. Run all necessary 

## 2. Thought process

Data pre-processing: Default training/validation split of 80/20
Ultralytics implements default 

## 3. Known limitations

Mallet performs worse than bottle. I did not fully find out why (small size, or confusion with bottle).
The confidence level was chosen on one dataset and may not be the best value for new images.
Large rotation makes the bounding boxes looser, so it can add background and cause some false positives.
The dataset is small, so the results may be noisy and the model may overfit.
I trained on a laptop, so I could not try many settings. With more time I would run a proper error analysis on the mallet misses, try K-fold cross validation, and test other image sizes.