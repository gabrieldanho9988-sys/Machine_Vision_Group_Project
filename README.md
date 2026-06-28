# Galactic Credit Scanner: Machine Vision & AI System

**Group 5 — Sensor Technology and Image Analysis, University West (Högskolan Väst)**

> *"Hours planning, avoid you must, if weeks of coding you crave."* — Master Yoda

---

## Overview

The year is 35 ABY, and counterfeit Galactic Credits are flooding the Outer Rim. The Galactic Banking Clan has commissioned an automated machine vision system to scan, identify, and authenticate 3D-printed Star Wars credit chips on a high-speed conveyor belt — in real time.

This project was developed for our **Sensor Technology and Image Analysis** master's course. It combines classical computer vision techniques with a trained CNN to detect, classify, and sum credit chip values from a live video feed.

---

## Objectives

| Objective | Description |
|---|---|
| **Real-Time Detection** | Detect and track credit chips dynamically on a moving conveyor belt |
| **Color & Pattern Recognition** | Identify chips by color tier (Red, Blue, Yellow) and geometric dot patterns |
| **Counterfeit Detection** | Flag fake credits using physical dimension analysis and logic validation |
| **Live Summation** | Calculate and display the running total of all authentic credits on-screen |

---

## Technical Stack

- **Python 3** + **Jupyter Notebook** (Google Colab)
- **OpenCV** — image acquisition, segmentation, HSV masking, and contour analysis
- **TensorFlow / Keras** — CNN model for credit value prediction
- **NumPy / Pandas** — data handling and label management
- **Scikit-learn** — label encoding and train/test splitting

---

## Dataset

We captured a balanced dataset of **2,800 images** directly from a webcam using a custom Python script:

| Color | Chips | Images per Chip | Total |
|---|---|---|---|
| Red | 6 | 150 | 900 |
| Blue | 5 | 200 | 1,000 |
| Yellow | 3 | 300 | 900 |

Images are named using the convention `Value_Index.png` (e.g., `9360_150.png`), which serves as the ground truth label for training.

**Credit denominations (14 classes):**
`0, 1, 5, 10, 21, 25, 42, 326, 756, 940, 956, 1540, 5520, 9360`

---

## Pipeline

### Phase 1 — Data Collection

A command-line capture script (`capture.py`) automates the process:

```bash
python capture.py --label 9360 --color yellow
```

- Press **SPACE** to toggle recording on/off
- Press **ESC** to stop
- Files auto-save as `{Value}_{Index}.png` with per-color quantity targets

### Phase 2 — Import & Validation

Raw images are imported from Google Drive. Path validation with try/catch ensures the dataset folder exists and is non-empty before processing begins.

### Phase 3 — Image Preprocessing & Standardization

Each raw image passes through a 10-step pipeline:

1. **HSV Conversion** — separates color from brightness for robust detection
2. **Darkness Mask** — isolates dark chips from the bright green conveyor (V < 90)
3. **Morphological Cleaning** — removes noise (OPEN) and fills holes (CLOSE)
4. **Contour Detection** — finds boundaries of all shapes in the mask
5. **Aspect Ratio Filter** — rejects circular glare spots (ratio < 1.5)
6. **Rotation Correction** — standardizes every chip to horizontal orientation
7. **ROI Cropping** — extracts just the chip with a 10px padding
8. **Grayscale Conversion** — reduces data by 3x (color isn't needed for dot counting)
9. **Orientation Fix** — compares dot density on left vs. right half; flips 180° if backwards
10. **Save** — outputs a clean, standardized grayscale image

### Phase 4 — Advanced Segmentation ("Double-Trap" Logic)

Basic detection failed on Gold/Yellow chips (they blend into the green background). We engineered a two-stage masking strategy:

- **Trap A (Darkness Mask)** — catches Red and Blue chips via low brightness (V < 95)
- **Trap B (Color Mask)** — targets Orange/Gold hue (H: 15–48, V ≥ 96) to capture Yellow chips

```python
chip_mask = cv2.bitwise_or(mask_dark, mask_yellow)
```

This phase also measures **physical dimensions** (length and thickness) of every detected chip, generating a reference dictionary used later for counterfeit detection:

```python
{'Blue':   {'len_min': 116.5, 'len_max': 231.0, 'thick_min': 54.7, 'thick_max': 57.1},
 'Yellow': {'len_min': 114.6, 'len_max': 180.4, 'thick_min': 54.0, 'thick_max': 63.6},
 'Red':    {'len_min': 114.8, 'len_max': 226.0, 'thick_min': 54.0, 'thick_max': 57.2}}
```

### Phase 5 — Label Generation

The file naming convention is parsed to auto-generate `training_labels.csv`:

```
9360_150.png  →  split('_')[0]  →  label: 9360
```

This maps all 2,800 images to their integer credit values.

### Phase 6 — CNN Training

A Convolutional Neural Network serves as the value recognition engine:

| Layer | Details | Purpose |
|---|---|---|
| Conv2D (32 filters) | 3×3 kernel, ReLU | Detect edges and curves |
| MaxPooling2D | 2×2 | Downsample feature maps |
| Conv2D (64 filters) | 3×3 kernel, ReLU | Detect shapes and corners |
| MaxPooling2D | 2×2 | Downsample |
| Conv2D (64 filters) | 3×3 kernel, ReLU | Detect complex dot patterns |
| MaxPooling2D | 2×2 | Downsample |
| Flatten | — | Convert 2D maps to 1D vector |
| Dense (64) | ReLU | Classification decision layer |
| Dropout (0.5) | — | Regularization (forces robust learning) |
| Dense (14) | Softmax | Output probability per class |

**Input:** 128×64 grayscale images, normalized to [0, 1]

**Training results (10 epochs):**

| Metric | Training | Validation |
|---|---|---|
| Accuracy | ~91% | **100%** |
| Loss | 0.22 | 0.014 |

Training accuracy appears lower due to **Dropout** — 50% of neurons are disabled during training, forcing robustness. At validation time, all neurons are active, yielding perfect accuracy. This is expected behavior for high-contrast geometric pattern recognition (counting white dots on black).

### Phase 7 — Live Scanning Engine ("Grand Master")

The deployment script integrates all components into a real-time loop:

```
Video Frame → Double-Trap Detection → Dimension Check → Color Classification → CNN Prediction → Running Total
```

1. **Detect** — segment chips from the green background using combined masks
2. **Verify** — measure thickness against reference dictionary; flag anomalies as `FAKE`
3. **Classify** — determine color tier (Red/Blue/Yellow) via glare-resistant HSV thresholds
4. **Predict** — crop, orient, and feed to CNN for value prediction
5. **Sum** — accumulate values and render the total on the live feed

Visual output: Green boxes = authenticated chips, Red boxes = fake/invalid chips.

### Phase 8 — Stress Test (Batch Processing)

Final validation against a **1,050-image test dataset** with two security gates:

| Gate | Check | Example |
|---|---|---|
| **Gate 1: Physical** | Thickness and length within reference ranges | Too thin → `FAKE` |
| **Gate 2: Logic** | Predicted value must match the color tier | Blue chip reading 9360 (a Yellow value) → `FAKE` |

**Color-to-value rules:**

| Color | Allowed Values |
|---|---|
| Yellow | 9360, 5520, 1540, 956, 940 |
| Blue | 756, 326, 42 |
| Red | 1, 5, 10, 21, 25 |

---

## Project Structure

```
├── capture.py                # Phase 1: Webcam data collection script
├── notebook.ipynb            # Phases 2–8: Full pipeline (Colab notebook)
├── dataset/
│   ├── raw_dataset/          # 2,800 raw webcam images
│   ├── dimentions_dataset/   # Cleaned, standardized grayscale images
│   └── test_dataset/         # 1,050 stress-test images
├── models/
│   ├── credit_classifier.h5  # Trained CNN model
│   └── label_classes.npy     # Label encoder (class ID → credit value)
├── training_labels.csv       # Image-to-value mappings
├── test_results/             # Annotated output images from batch scan
└── README.md
```

*Adjust paths if your layout differs.*

---

## Getting Started

### Prerequisites

```bash
pip install opencv-python tensorflow numpy pandas matplotlib scikit-learn
```

### 1. Collect Data (optional — skip if using existing dataset)

```bash
python capture.py --label 9360 --color yellow
```

### 2. Run the Full Pipeline

Open the notebook in Google Colab, mount your Google Drive, and run all cells sequentially.

### 3. Live Scanning

The final cells launch the scanning engine against test images. For real-time webcam scanning, update the camera index in the script to match your hardware.

> **Note:** A webcam, 3D-printed credit chips, and a green conveyor belt are required for the live demo.

---

## Team

**Group 5** — University West (Högskolan Väst), Master's Programme, Sensor Technology and Image Analysis

---

## License

This project was created for educational purposes as part of a university course.
