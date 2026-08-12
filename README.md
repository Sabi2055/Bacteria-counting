# Bacteria-Counting: AI-Driven Single Bacterial-Cell Detection for Rapid Pathogen Diagnostics

Deep-learning (U-Net) pipeline for automated segmentation, counting, and antimicrobial susceptibility testing (AST) of individual bacterial cells from phase-contrast microscopy images. Developed for an integrated microfluidic + AI diagnostic platform that determines minimum inhibitory concentrations (MICs) within 2 hours, versus 24–48 hours for standard broth microdilution.

> Based on: *"Integration of artificial intelligence and single bacterial-cell detection for rapid, automated pathogen diagnostics in severe infections."* Sabita Khadka, Bushra Raahat, Spyros Tragoudas, Hui Li. School of Electrical, Computer, and Biomedical Engineering, Southern Illinois University, Carbondale, IL, USA.

## Overview

Conventional pathogen identification and AST rely on overnight culture-based methods that take 24–48 hours, delaying appropriate antibiotic therapy and contributing to antimicrobial resistance. This project pairs a microfluidic device — which confines individual bacterial cells under controlled antibiotic exposure for time-lapse phase-contrast imaging — with a U-Net convolutional neural network that automatically segments and counts bacteria, replacing slow, error-prone manual image analysis.

Key results from the associated study:

- **100%** training accuracy and validation accuracy (500 epochs), with training/validation loss of 0.0056 / 0.0067
- **Dice coefficient 0.8437**, **Jaccard index 0.7297**, recall 0.836, precision 0.8516
- **96% ± 1%** of individual *E. coli* cells correctly identified on an independent test set, with a low false-positive rate on bacteria-free images
- Dose-dependent AST for *E. coli* against ciprofloxacin (MIC 0.016 µg/mL) and trimethoprim/sulfamethoxazole (MIC 0.125/2.375 µg/mL), matching standard broth microdilution
- Extended to clinically relevant conditions: 100% *E. coli* and 91% *S. aureus* correctly identified in the presence of blood cells, with no blood cells misclassified as bacteria

## Repository Structure

```
Bacteria-counting/
├── Assignment.ipynb          # U-Net training / inference notebook
├── Location1/                 # Phase-contrast TIFF images (raw microscopy data)
│   ├── Data Folder/
│   └── ...
├── requirements.txt           # Python dependencies
└── README.md
```

*(Additional `LocationN/` folders will be added as more experimental data is released; see [Data](#data) below.)*

## Method Summary

**Microfluidic device:** Glass substrate + laser-cut acrylic + double-sided adhesive tape channels (200 µm × 5 µm × 20 mm), enabling single bacterial-cell confinement for stable time-lapse imaging on an Olympus IX73 microscope (40× objective, DP23M CMOS camera).

**Model:** Fully convolutional U-Net (encoder–decoder with skip connections), trained with the Adam optimizer, binary cross-entropy loss, learning rate 0.0001, on dual NVIDIA RTX A5500 GPUs.

**Dataset:**  ~1,600 - 1,700 annotated *E. coli* cells plus negative-control and *S. aureus* images, from 19 full-resolution microscopy images (2076 × 3088 px) split into 7,296 patches of 128 × 128 px. Ground-truth masks were manually annotated in ImageJ. Split: 80% train / 20% validation.

**Quantification:** Connected-component labeling on U-Net output masks, with size-threshold filtering (within 95% confidence interval mean ± 2 SD of manually measured single-cell area ) for *E. coli*; watershed segmentation + area-based proportional assignment for clustered *S. aureus* cocci.

## Data

The `Location*/` folders contain raw phase-contrast TIFF images from microfluidic AST experiments. Each location corresponds to a distinct imaging session/condition (e.g., antibiotic concentration series, clinical-matrix samples).


## Model Weights

Trained U-Net weights is in Model21.h5. Instructions for loading the weights into `Bacteria identification_and_counting.ipynb` will be added alongside the release.

## Requirements

```
python>=3.9
tensorflow>=2.x  (or pytorch, depending on implementation)
numpy
opencv-python
scikit-image
matplotlib
jupyter
```

Install with:
```bash
pip install -r requirements.txt
```

## Usage

1. Clone the repository and install dependencies.
2. Open `Bacteria identification_and_counting.ipynb` in Jupyter.
3. Point the notebook to your image directory (e.g., `Location1/`).
4. Run cells to preprocess images (denoising, intensity normalization, patching), train or load the U-Net model, and generate bacterial counts / growth curves.

## Citation

If you use this code or data, please cite:

```
Khadka, S., Raahat, B., Tragoudas, S., & Li, H. Integration of artificial
intelligence and single bacterial-cell detection for rapid, automated
pathogen diagnostics in severe infections. (manuscript in preparation).
```

## Authors

- Sabita Khadka¹
- Bushra Raahat¹
- Spyros Tragoudas¹ (corresponding author)
- Hui Li¹ (corresponding author)

¹ School of Electrical, Computer, and Biomedical Engineering, Southern Illinois University, Carbondale, IL, USA

## Acknowledgments

This work was supported by the Southern Illinois University (SIU) startup fund and the SIU System Collaborative Grant Award.


