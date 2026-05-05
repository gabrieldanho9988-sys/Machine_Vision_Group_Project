# 🌌 Galactic Credit Scanner: Machine Vision & AI System (Group 5)

## 📖 Background Story
The year is 35 ABY, and the galaxy is in economic turmoil[cite: 1]. With counterfeit Galactic Credits circulating in the Outer Rim, the Galactic Banking Clan has commissioned our team to build an elite, automated machine vision system[cite: 1]. 

This project was developed for our **Sensor Technology and Image Analysis** course. The goal is to scan, identify, and track 3D-printed Star Wars credit chips as they move along a high-speed conveyor belt, ensuring the stability of the New Republic's economy[cite: 1].

## 🎯 Mission Objectives
*   **Real-Time Processing:** Detect and track credit chips dynamically on a moving conveyor belt[cite: 1].
*   **Color & Pattern Detection:** Identify chips by their color and geometric patterns to determine their specific denomination[cite: 1].
*   **Counterfeit Detection:** Differentiate between real and fake credits[cite: 1].
*   **Dynamic Summation:** Calculate and display the total value of all authentic credits on-screen in real-time[cite: 1].

## 🛠️ Technical Approach & Workflow
Our team built this system utilizing Python, OpenCV, and custom AI models[cite: 1]. We structured our development pipeline in a Jupyter Notebook (`Machine_Vision_Project_Group_5.ipynb`) to manage everything from dataset generation to the final tracking logic.

### Phase 1: Data Collection (Raw Dataset Generation)
Before building our AI or logic, we needed a robust, balanced dataset. We wrote a custom Python script using `cv2` and `os` to automate the collection process directly from a webcam.

*   **Automated Labeling & Naming:** The script takes a label as an input argument and saves every frame in a structured format: `Value_Index.png` (e.g., `9360_150.png`). This strict naming convention acts as the "Answer Key" for our AI training phase.
*   **Rapid Capture:** Captures frames continuously while a user holds and rotates the chip to ensure varied angles and lighting.
*   **Quantity Control & Balanced Dataset:** We set specific target limits to prevent class imbalance. Our custom dataset consists of 2,800 total images:
    *   **Red Chips:** 6 chips × 150 images = 900 images
    *   **Blue Chips:** 5 chips × 200 images = 1000 images
    *   **Yellow Chips:** 3 chips × 300 images = 900 images

## 🚀 How to Run (Coming Soon)
*(Instructions on how to install dependencies and execute the live scanner will be added here upon project completion).*

> *"Hours planning, avoid you must, if weeks of coding you crave."* - Master Yoda[cite: 1]
