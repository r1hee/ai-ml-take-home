# Methodology

Replace this template with your own. Keep it short: write what a teammate would need to run your work and trust it.

# Methodology

## 1. How to run it

**Tested on:** macOS  (MacBook M4, Apple GPU via `mps`), Python `<3.12.7>`. On Linux or Windows with an NVIDIA GPU, change `device="mps"` to `device=0`. On CPU only, use `device="cpu"` (much slower).

**1. Clone and install** (one script, creates a virtual environment and installs everything):


```bash
git clone <repo-url>
cd <repo-name>
python -m venv .venv
source .venv/bin/activate
```

**Open task.ipynb**
**2. Run everything** (the whole notebook installs dependencies, downloads the data, trains, evaluates, and runs inference on a few sample images):
- The dataset is downloaded from Roboflow so replace "ROBOFLOW_API_KEY" with your own API key
- Skip the training block if going to just test inference


## 2. Thought process

- **Confidence level.** I chose a confidence threshold of `0.45` for predictions. A low threshold finds more objects but gives more false positives. A high threshold gives fewer false positives but misses more objects. I used the recall-confidence curve to pick it. Recall stays high until a confidence of about 0.5 and then drops quickly, so I kept the threshold at or below that range to avoid missing objects.
- **Preprocessing.** I skimmed the data to make sure labeling on the dataset was correct. Then, I kept the default pre-processing that Ultralytics did which included color format conversion to RGB, pixel normalization, resizing to given image size, and the default augmentation set including blur, and median blur. I did this so every image goes into the model at the same size and the labels match the images. 
- **Augmentation.** The dataset is very small (999 images), so I used augmentation to get more out of it. I kept the default augmentations (Fliplr, HSV color jitter, translation and scaling)that come with YOLOv8 and turned mosaic off (as objects in the dataset were already very small). I also added occlusion (random patches that hide part of an image), so objects are sometimes partly hidden and the model does not rely on one part of an object.
- **Loss.** YOLOv8 trains on box loss (how far the box is from the real one), class loss (bottle vs mallet), and DFL loss (box edge placement). I set `cls=1.5` instead of the default 0.5 to put more weight on the class loss as the model often misclassified mallets as bottles and I watched train and validation loss to check for overfitting.

## 3. Results

Evaluated on the validation set.

| Class | mAP@0.5 |
|---|---|
| bottle | 0.890 |
| mallet | 0.977 |
| **all** | **0.934** |

| Overall metric | Value |
|---|---|
| Precision | about 0.95 |
| Recall | about 0.87 |
| mAP@0.5-0.95 | about 0.61 |

Precision, recall, and mAP@0.5-0.95 are read from the training curves at epoch 70, so they are approximate.

**Recall-confidence curve.** Recall for all classes is 0.95 at the lowest confidence and stays above about 0.85 up to a confidence of 0.5. It falls fast after 0.6 to 0.7.

**Training curves.** Train and validation loss both go down and level off, with no sign of overfitting. mAP and train loss are still slowly improving at epoch 70.

## 4. Errors and known limitations

### Errors the model makes

- **Bottle is the weaker class.** Its mAP@0.5 is 0.890 vs 0.977 for mallet, and its recall is lower at low confidence (about 0.89 vs 0.93 to 0.98). It misses more bottles than mallets.
- **Boxes are not tight.** mAP@0.5 is 0.934 but mAP@0.5-0.95 is only about 0.61. The model finds the objects, but the box edges are not precise.
- **More misses than false alarms.** Recall (about 0.87) is lower than precision (about 0.95).
- **Mallet confidence is lower.** Above a confidence of about 0.7, mallet recall drops faster than bottle recall, so the model is less sure about its mallet predictions.

### Limitations and what I would do next

- I did not run a proper error analysis (looking at the actual missed bottles and wrong boxes), so the reasons above are guesses.
- The model was still improving at epoch 70, so it is probably a bit undertrained. More epochs could help.
- The confidence level was chosen on one dataset and may not be the best value for new images.
- The dataset is small, so the results may be noisy.
- I trained on a laptop, so I could not try many settings. With more time I would try K-fold cross validation, other image sizes, and a lower learning rate.

### Stretch goals
- The training images that my model is least confident on seems to be mallets. It had a problem with sometimes recognizing mallets as bottles, not even detecting it, or misrecognizing the background as objects. I think that in order to fix these problems the current data has to be thoroughly reviewed to have more tight bounding boxes. For images that were being misrecognized, I often noticed that it was in scenes with some of the object covered or in bad lighting. More scenes with these types of scenarios could be helpful in correcting some of the misclassification. 
- I got a parameter count of 3,157,200 parameters
- Computing inference speed = GFLOPs / (TOPS x utilization) = 8.9 GFLOPS / (6 TOPS x 0.3) = 4.94 ms. (This can get our ~200 frames of inferences in a second)
- the 6 TOPS computing power is based off the Orange Pi 5's computing power
- The inference speed on this model is quite fast based off predictions on relevant hardware. ~3 million parameters is a reltaively small model. Therefore, I think that I can sacrifice some time and size in order to increase the accuracy on this model to perform better on metrics like the mAP@0.5-0.95