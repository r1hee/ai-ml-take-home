# AI Usage Log

Replace this template with your own entries. Add one entry per significant use of an AI tool.

## 1. <short title of what you used it for>

**What I asked:** The model seems to struggle to detect mallets that are obstructed by plants or other environmental factors. What augmentation methods would help mitigate that?

**What I kept vs. rewrote, and why:** The AI told me to consider adding random occlusion patches to the data using Albumentations' CoarseDropout. It also suggested mosaic and scale + translate to push objects partly off-frame that mimics objects getting cutoff by the environmental factors.

**What the AI got wrong that I had to catch:** Nothing in particular. The occlusion patches did seem to help certain images which views were obstructed to perform better in the inference round.

**How I verified it (for example, checked the metric by hand, ran it on data it hadn't seen):**

## 2. <next use>

**What I asked:** How to write a good methodology on running my code.

**What I kept vs. rewrote, and why:** I kept the lines of code on running the virtual env and the steps up to running the notebook. I rewrote some parts about adding your own RoboFlow API key and details on which lines to skip for just getting the inference.

**What the AI got wrong that I had to catch:** At first, the AI had me make instructions for a non-jupyter notebook environment that I had to catch.

**How I verified it (for example, checked the metric by hand, ran it on data it hadn't seen):** I verified this by running the instruction myself and trying to see if they were clear enough to run step by step.
