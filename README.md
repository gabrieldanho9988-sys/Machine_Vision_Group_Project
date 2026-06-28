Galactic Credit Scanner: Machine Vision & AI System
Group 5 — Sensor Technology and Image Analysis, Malmö University

"Hours planning, avoid you must, if weeks of coding you crave." — Master Yoda

Overview
The year is 35 ABY, and counterfeit Galactic Credits are flooding the Outer Rim. The Galactic Banking Clan has commissioned an automated machine vision system to scan, identify, and authenticate 3D-printed Star Wars credit chips on a high-speed conveyor belt — in real time.

This project was developed for our Sensor Technology and Image Analysis course. It combines classical computer vision techniques with a trained CNN to detect, classify, and sum credit chip values from a live video feed.

Objectives
Objective	Description
Real-Time Detection	Detect and track credit chips dynamically on a moving conveyor belt
Color & Pattern Recognition	Identify chips by color tier (Red, Blue, Yellow) and geometric dot patterns
Counterfeit Detection	Flag fake credits using physical dimension analysis (length and thickness)
Live Summation	Calculate and display the running total of all authentic credits on-screen
Technical Stack
Python 3 + Jupyter Notebook
OpenCV — image acquisition, segmentation, and contour analysis
TensorFlow / Keras — CNN model for value prediction
NumPy / Pandas — data handling and label management
Pipeline
Phase 1 — Data Collection
Automated webcam capture with structured file naming (Value_Index.png) serving as ground truth labels. Produced a balanced dataset of 2,800 images across Red, Blue, and Yellow chips.

Phase 2 — Visual Verification & Cleaning
Helper functions for in-notebook image display. Ensured all chips were properly cropped, segmented from the background, and correctly oriented before model training.

Phase 3 — Advanced Segmentation ("Double-Trap" Logic)
Gold/Yellow chips blended into the green conveyor background, so basic thresholding failed. We engineered a two-stage detection algorithm:

Trap A (Darkness) — catches darker Red and Blue chips via intensity thresholding
Trap B (Color) — targets the specific Orange/Gold hue to segment Yellow chips
During detection, each chip's physical dimensions (length and thickness) are measured and stored as a reference dictionary for counterfeit verification.

Phase 4 — Dataset Structuring
Parsed the file naming convention to auto-generate training_labels.csv, mapping all 2,800 cropped images to their integer credit values.

Phase 5 — CNN Training
A Convolutional Neural Network acts as the value recognition engine:

Layer	Purpose
3 × Conv2D + MaxPooling	Extract spatial features (edges, dots, patterns)
Dropout (0.5)	Regularization to force robust feature learning
Dense layers	Final value classification
Results: Training accuracy ~84% (with dropout penalty active), validation accuracy 100%. The high-contrast white-dots-on-black pattern makes this a near-ideal task for CNNs.

Phase 6 — Live Scanning Engine
The deployment script integrates all components into a single real-time loop:

Video Frame → Segmentation (Double-Trap) → Dimension Check (Fake?) → Color Classification → CNN Prediction → Running Total
Detect — segment chips from the green conveyor background
Verify — measure dimensions against reference; flag anomalies as FAKE
Classify — determine color tier using glare-resistant HSV thresholds
Predict — crop and orient the chip, pass to the CNN (.h5 model) for value prediction
Sum — accumulate values of authenticated chips and render the total on the live feed
Project Structure
├── data/                     # Raw and processed chip images
├── models/                   # Trained CNN model (.h5)
├── training_labels.csv       # Image-to-value mappings
├── notebook.ipynb            # Full pipeline (collection → training → deployment)
└── README.md
Update the tree above to match your actual file layout.

Getting Started
Prerequisites
pip install opencv-python tensorflow numpy pandas matplotlib
Run the Scanner
jupyter notebook notebook.ipynb
Run all cells sequentially. The final cell launches the live camera feed with real-time credit scanning.

Note: A webcam and the 3D-printed credit chips on a green conveyor belt are required for the live demo.

Team
Group 5 — Malmö University, Sensor Technology and Image Analysis

License
This project was created for educational purposes as part of a university course.

Key improvements I made:
Added a table-based objectives section — much easier to scan than bullet points
Separated the technical stack into its own section so readers immediately know what's used
Simplified pipeline descriptions — removed redundant phrasing, tightened language
Added a pipeline flow diagram (the → chain) for a quick visual understanding
Added a project structure tree — critical for any repo README
Added a Getting Started section with actual install/run commands
Removed "Coming Soon" — replaced with concrete instructions
Renumbered phases sequentially (the original jumped from Phase 1 to Phase 3)
Moved the Yoda quote to the top as an epigraph instead of burying it at the bottom
