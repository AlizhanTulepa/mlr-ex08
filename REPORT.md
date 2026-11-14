# REPORT: Task 4

## Setup
- Data: 4 leaf classes, 37 validation images (one image = 2.7 percentage points).
- Baseline: my Task 3 network LeafNet, Adam, lr 1e-3, 15 epochs.
- Every variant is trained on the same training images; seeds 0, 1, 2; the table shows mean ± std over the 3 seeds.
- "val acc" is the mean of the last 5 epochs. "camera-like acc" is the accuracy after the last epoch
  on the validation images randomly cropped (50-100 %), rotated (up to 30°) and with changed
  brightness, contrast, saturation and hue; generated once with a fixed seed (123).
- One change at a time against the baseline, except the last row, which combines three changes.

## Results

| variant | val acc | camera-like acc |
|---|---|---|
| baseline LeafNet | 0.715 ± 0.029 | 0.414 ± 0.034 |
| + geometric augmentation | 0.665 ± 0.028 | 0.459 ± 0.038 |
| + geometric + colour augmentation | 0.631 ± 0.029 | 0.441 ± 0.046 |
| pretrained MobileNetV3-small, last layer only | 0.872 ± 0.018 | 0.667 ± 0.034 |
| pretrained, fine-tuned (all layers, lr 3e-4) | 0.834 ± 0.038 | 0.739 ± 0.046 |
| pretrained + augmentation + cosine schedule, 25 epochs | 0.861 ± 0.046 | 0.847 ± 0.046 |

## What each change brought
- **Augmentation on my own network:** val acc went down (0.715 → 0.665 → 0.631), camera-like acc
  went up a little (0.414 → 0.459), colour jitter added nothing (0.441). The network is small and
  trained for only 15 epochs, so harder training images mostly slow it down.
- **Pretrained network, last layer only:** the biggest single gain: +16 points on val (0.872) and
  +25 points on camera-like (0.667) against the baseline.
- **Fine-tuning all layers:** val acc is not better than last-layer-only (0.834 against 0.872),
  cam
