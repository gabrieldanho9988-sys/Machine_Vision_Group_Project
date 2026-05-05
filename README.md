# 🌌 Galactic Credit Scanner: Machine Vision & AI System (Group 5)

## 📖 Background Story
The year is 35 ABY, and the galaxy is in economic turmoil. With counterfeit Galactic Credits circulating in the Outer Rim, the Galactic Banking Clan has commissioned our team to build an elite, automated machine vision system. 

This project was developed for our **Sensor Technology and Image Analysis** course. The goal is to scan, identify, and track 3D-printed Star Wars credit chips as they move along a high-speed conveyor belt, ensuring the stability of the New Republic's economy.

## 🎯 Mission Objectives
*   **Real-Time Processing:** Detect and track credit chips dynamically on a moving conveyor belt.
*   **Color & Pattern Detection:** Identify chips by their color and geometric patterns to determine their specific denomination.
*   **Counterfeit Detection:** Differentiate between real and fake credits using precise physical dimensional analysis.
*   **Dynamic Summation:** Calculate and display the total value of all authentic credits on-screen in real-time.

## 🛠️ Technical Approach & Pipeline
Our team built this system utilizing Python, OpenCV, and custom AI models. We structured our development pipeline in a Jupyter Notebook to manage everything from dataset generation to the final tracking logic.

### Phase 1: Data Collection (Raw Dataset Generation)
Before building our AI or logic, we needed a robust, balanced dataset. We wrote a custom Python script using `cv2` and `os` to automate the collection process directly from a webcam.
*   **Automated Labeling & Naming:** Saved every frame in a structured format: `Value_Index.png` (e.g., `9360_150.png`). This strict naming convention acts as the "Answer Key" for our AI.
*   **Quantity Control:** Captured a balanced dataset of 2,800 images across Red, Blue, and Yellow chips.

### Phase 3 & 4: Visual Verification & Cleaning
We implemented helper functions to display images directly within the notebook, essential for visual verification at each step. This phase ensured all chips were perfectly cropped, segmented from the background, and in the correct orientation before advancing.

![Final Validated Chip](Drag-and-drop-your-validated-chip-image-here.png)
*(Example of a preprocessed, automatically cropped, and oriented chip ready for the AI).*

### Phase 5: Advanced Data Curation ("Double-Trap" Logic)
Basic detection struggled with the Gold/Yellow chips blending into the green conveyor background. We engineered a more advanced "Double-Trap" algorithm:
*   **Trap A (Darkness):** Specifically catches the darker Red and Blue chips.
*   **Trap B (Color):** Targets the specific Orange/Gold hue to ensure Yellow chips are accurately segmented.
*   **Ground Truth Extraction:** As chips are detected, the system measures their physical dimensions (length and thickness). This generated a reference dictionary vital for our counterfeit "Security Gate" later on.

### Phase 6: Dataset Structuring
We parsed our rigorous file naming convention to automatically generate a `training_labels.csv`. This mapped all 2,800 clean, perfectly cropped images to their exact integer values, prepping the data for the neural network.

### Phase 7: Training the "Brain" (CNN Model)
We built and trained a Convolutional Neural Network (CNN) to act as the primary recognition engine for the credit values.
*   **Architecture:** 3 Convolutional Layers to extract features (edges, dots, geometric patterns), followed by Dense Layers for the final decision.
*   **The "Dropout" Effect:** We utilized `Dropout(0.5)` during training, handicapping the model to force it to learn robust, generalizable features. 
*   **Performance:** While training accuracy hovered around ~84% due to the dropout penalty, validation accuracy hit an expected **100%**. Because the task relies on counting highly distinct, high-contrast geometric white dots on a black background, the CNN operates flawlessly on unseen validation data.

![CNN Accuracy and Loss Graphs](Drag-and-drop-your-accuracy-loss-graphs-image-here.png)
*(Training vs. Validation metrics showcasing a highly robust and generalizable "Grand Master" level model).*

### Phase 8: The "Grand Master" Scanning Engine
The final deployment script integrates every component into a single, real-time loop:
1.  **Detection:** Uses "Double-Trap" logic to segment chips from the green background.
2.  **Verification (Fake Detection):** Measures physical dimensions. If a chip is too thin or thick based on our Phase 5 reference dictionary, it is instantly flagged as **FAKE**.
3.  **Classification:** Determines the color tier (Red, Blue, Yellow) using glare-resistant thresholds.
4.  **AI Reading:** If real, the image is cropped, oriented, and passed to our trained CNN (.h5 model) to predict the exact credit value.
5.  **Totaling:** Sums up the values of all valid, authenticated chips on screen and renders the dynamic total directly on the live video feed.

![Live Scanner Engine Output](Drag-and-drop-your-green-screen-scanner-output-image-here.png)
*(The final system in action: detecting color, identifying the chip value via the CNN, and rendering the dynamic total on-screen).*

## 🚀 How to Run (Coming Soon)
*(Instructions on how to install dependencies and execute the live scanner will be added here upon project completion).*

> *"Hours planning, avoid you must, if weeks of coding you crave."* - Master Yoda
