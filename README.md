🖐️ ESP32 TinyML Gesture Control

Real-Time Hand Gesture Recognition using Computer Vision + Machine Learning + TinyML + ESP32

<p align="center">
  <img src="https://img.shields.io/badge/ESP32-TinyML-blue?style=for-the-badge&logo=espressif" alt="ESP32">
  <img src="https://img.shields.io/badge/TensorFlow-Lite-orange?style=for-the-badge&logo=tensorflow" alt="TensorFlow">
  <img src="https://img.shields.io/badge/Python-3.x-yellow?style=for-the-badge&logo=python" alt="Python">
  <img src="https://img.shields.io/badge/OpenCV-Computer%20Vision-green?style=for-the-badge&logo=opencv" alt="OpenCV">
  <img src="https://img.shields.io/badge/MediaPipe-Hand%20Tracking-purple?style=for-the-badge" alt="MediaPipe">
  <img src="https://img.shields.io/badge/Arduino-ESP32-00979D?style=for-the-badge&logo=arduino" alt="Arduino">
</p>

<p align="center">
  <b>Turn a human hand gesture into an AI prediction on an ESP32 — and use that prediction to control real hardware.</b>
</p>

📌 Overview

ESP32 TinyML Gesture Control is an end-to-end Embedded AI project that combines:

👁️ Computer Vision

✋ Hand Landmark Detection

🧠 Machine Learning

⚡ TinyML

🔌 ESP32 Embedded Systems

⚙️ Servo Motor Control

A laptop webcam captures the user's hand. MediaPipe detects the hand and extracts its 21 landmarks. These landmarks are converted into a compact 63-value feature vector and sent to the ESP32 over USB serial.

The ESP32 runs a lightweight neural network using TensorFlow Lite Micro, predicts the gesture, and converts the AI result into a physical action using a servo motor and LED.

The complete pipeline

                HUMAN HAND
                     │
                     ▼
               USB WEBCAM
                     │
                     ▼
                  OpenCV
                     │
                     ▼
                MediaPipe
                     │
             21 Hand Landmarks
                     │
                     ▼
           Feature Normalization
                     │
              63 Features
                     │
                     ▼
                USB Serial
                     │
                     ▼
                  ESP32
                     │
            TensorFlow Lite
                  Micro
                     │
                     ▼
            Gesture Prediction
                     │
              ┌──────┴──────┐
              ▼             ▼
         Servo Motor      LED

✨ Project Highlights

Feature

Description

🖐️ Gesture Recognition

Recognizes four predefined hand gestures

👁️ Computer Vision

Uses OpenCV + MediaPipe for hand tracking

🧠 Neural Network

Lightweight fully connected classifier

⚡ TinyML

Neural network inference runs on ESP32

🔢 63 Features

21 landmarks × X/Y/Z coordinates

🔌 Serial Communication

Laptop sends normalized features to ESP32

⚙️ Hardware Control

Gesture prediction controls a servo and LED

🛡️ Confidence Filtering

Low-confidence predictions can be rejected

📦 Model Deployment

TensorFlow → TFLite → C header → ESP32

🎯 Objective

The objective is to demonstrate the complete journey of a machine learning model from data collection to embedded deployment.

COLLECT
   ↓
PREPROCESS
   ↓
TRAIN
   ↓
EVALUATE
   ↓
CONVERT
   ↓
DEPLOY
   ↓
INFER
   ↓
ACT

The project is designed to show students and developers how AI can move from a Python development environment into a resource-constrained microcontroller.

🖐️ Supported Gestures

The current classifier recognizes four gesture classes:

Class

Gesture

Servo Position

0

✊ FIST

0°

1

☝️ ONE

30°

2

✌️ TWO

90°

3

✋ PALM

150°

The exact actuator behavior can be changed in the ESP32 firmware.

🧠 How the AI Works

1. Capture the Hand

The laptop webcam continuously captures video frames.

Webcam
   ↓
Video Frame
   ↓
OpenCV

OpenCV is responsible for accessing the camera and handling the video stream.

2. Detect the Hand

MediaPipe detects the hand and provides 21 landmarks.

Each landmark contains:

X coordinate
Y coordinate
Z coordinate

Therefore:

21 landmarks × 3 coordinates
= 63 numerical features

The model input is therefore:

Input Shape = 63

📍 Hand Landmark Representation

Conceptually, the hand is represented as:

                 WRIST
                   ●
                /     \
               ●       ●
              /         \
             ●           ●
            /             \
       Finger joints → Finger tips

MediaPipe converts the visual hand into numerical information.

Instead of sending an entire image to the ESP32, the project sends only the extracted hand features.

This makes the embedded inference pipeline much lighter.

📐 Feature Normalization

Raw hand coordinates depend on:

Where the hand is located in the image

Distance from the camera

Size of the hand in the frame

To reduce this dependency, the landmarks are normalized relative to the wrist and hand scale.

Conceptually:

Raw Coordinates
       ↓
Wrist Reference
       ↓
Relative Coordinates
       ↓
Scale Normalization
       ↓
63-Dimensional Feature Vector

This allows the model to focus more on the shape and relative arrangement of the hand.

📊 Dataset Creation

The project includes a Python dataset-generation stage.

Run:

python generate_dataset.py

The program collects samples for:

FIST
ONE
TWO
PALM

Each sample contains:

63 numerical features
+
1 gesture label

The dataset is stored in:

gesture_data.csv

Conceptually:

Feature_1, Feature_2, ... Feature_63, Label
0.12,      0.04,       ... 0.31,      FIST
0.10,      0.06,       ... 0.28,      FIST
...

🤖 Neural Network

The classifier is intentionally small so that it can be deployed on a microcontroller.

Architecture

              INPUT
           63 Features
                │
                ▼
       ┌─────────────────┐
       │   Dense Layer   │
       │    24 Neurons   │
       │      ReLU       │
       └────────┬────────┘
                │
                ▼
       ┌─────────────────┐
       │   Dense Layer   │
       │    12 Neurons   │
       │      ReLU       │
       └────────┬────────┘
                │
                ▼
       ┌─────────────────┐
       │   Output Layer  │
       │     4 Neurons   │
       │     Softmax     │
       └────────┬────────┘
                │
                ▼
         Gesture Class

Model configuration

Input features      : 63
Hidden layer 1      : 24 neurons
Hidden layer 2      : 12 neurons
Output classes      : 4
Hidden activation   : ReLU
Output activation   : Softmax
Optimizer           : Adam
Loss function       : Sparse Categorical Crossentropy

🧪 Model Training

Run:

python train_tinyml.py

The training script loads the collected dataset and trains the gesture classifier.

The trained model is exported as:

gesture_model.tflite

The important transition is:

gesture_data.csv
       ↓
TensorFlow / Keras
       ↓
Trained Neural Network
       ↓
gesture_model.tflite

📦 TensorFlow Lite Conversion

The trained neural network is converted into a TensorFlow Lite model for efficient deployment.

TensorFlow Model
       ↓
TensorFlow Lite
       ↓
gesture_model.tflite

TensorFlow Lite provides a compact representation suitable for embedded inference.

🔄 Convert the Model into a C Header

The ESP32 firmware needs the model to be available as compiled data.

Run:

python convert_tflite_to_header.py

This generates:

gesture_model.h

The model is represented as a C/C++ byte array:

const unsigned char gesture_model_tflite[] = {
    ...
};

The header can then be included in the ESP32 firmware.

⚡ TinyML on ESP32

The ESP32 uses TensorFlow Lite Micro to execute the neural network.

The embedded inference flow is:

63 Features
     ↓
Input Tensor
     ↓
TensorFlow Lite Micro
     ↓
Neural Network
     ↓
Output Tensor
     ↓
4 Class Probabilities
     ↓
Highest Probability
     ↓
Gesture

For example:

FIST  → 0.02
ONE   → 0.04
TWO   → 0.91
PALM  → 0.03

The ESP32 selects:

Prediction = TWO
Confidence = 91%

🛡️ Confidence Threshold

The project uses a confidence threshold to reduce unwanted actions caused by uncertain predictions.

Example:

Confidence = 91%
Threshold  = 70%

→ Accept prediction
→ Move servo

But:

Confidence = 43%
Threshold  = 70%

→ Reject prediction
→ Keep previous actuator state

This is a simple but useful technique for improving the stability of real-time embedded AI systems.

⚙️ Hardware Control

Once the gesture is accepted, the ESP32 converts the prediction into an actuator command.

             AI PREDICTION
                    │
        ┌───────────┴───────────┐
        │                       │
        ▼                       ▼
   SERVO CONTROL            LED CONTROL

Gesture Mapping

✊ FIST
   ↓
Servo → 0°

☝️ ONE
   ↓
Servo → 30°

✌️ TWO
   ↓
Servo → 90°

✋ PALM
   ↓
Servo → 150°

The onboard LED can also be used as a visual indication of accepted predictions.

🔌 Hardware Requirements

Main Components

ESP32 DevKit / ESP32-WROOM

SG90 or compatible servo motor

USB cable

Laptop / PC

Webcam

Jumper wires

External 5V servo supply recommended

🔗 Pin Configuration

Component

ESP32 Pin

Servo Signal

GPIO 18

Onboard LED

GPIO 2

USB Serial

USB

⚠️ Servo power recommendation: Do not rely on the ESP32 3.3V rail for a servo that may draw significant current. Use a suitable external 5V supply and connect the external supply ground to ESP32 GND.

🖥️ Software Requirements

Recommended Python

Python 3.10+

Python Packages

pip install opencv-python mediapipe numpy pandas scikit-learn tensorflow pyserial

Or use:

pip install -r install_requirements.txt

Arduino

Install:

Arduino IDE

ESP32 board package

ESP32Servo library

TensorFlow Lite Micro / compatible ESP32 TensorFlow Lite library

Select:

Board → ESP32 Dev Module

📁 Project Structure

ESP32_AI_Gesture_Control/
│
├── generate_dataset.py
│
├── train_tinyml.py
│
├── convert_tflite_to_header.py
│
├── laptop_gesture_serial.py
│
├── gesture_data.csv
│
├── gesture_model.tflite
│
├── gesture_model.h
│
├── install_requirements.txt
│
├── wiring.txt
│
└── esp32_tinyml_gesture/
    │
    ├── esp32_tinyml_gesture.ino
    │
    └── gesture_model.h

▶️ Getting Started

Step 1 — Clone the Repository

git clone https://github.com/YOUR_USERNAME/ESP32_AI_Gesture_Control.git
cd ESP32_AI_Gesture_Control

Replace YOUR_USERNAME with your GitHub username.

Step 2 — Install Python Dependencies

pip install -r install_requirements.txt

If the requirements file is not used:

pip install opencv-python mediapipe numpy pandas scikit-learn tensorflow pyserial

Step 3 — Generate the Dataset

Run:

python generate_dataset.py

Follow the instructions shown by the program.

Perform each gesture in front of the camera.

The generated dataset will be saved as:

gesture_data.csv

Step 4 — Train the Model

Run:

python train_tinyml.py

After training, the TensorFlow Lite model will be generated:

gesture_model.tflite

Step 5 — Convert the Model

Run:

python convert_tflite_to_header.py

The output will be:

gesture_model.h

Copy the generated header into the ESP32 Arduino sketch folder if required.

Step 6 — Upload ESP32 Firmware

Open:

esp32_tinyml_gesture/esp32_tinyml_gesture.ino

Make sure the model header is available:

esp32_tinyml_gesture/
├── esp32_tinyml_gesture.ino
└── gesture_model.h

Select:

Tools → Board → ESP32 Dev Module

Select the correct COM port and upload the firmware.

Step 7 — Find the ESP32 COM Port

In Arduino IDE:

Tools → Port

Identify the port assigned to the ESP32.

For example:

COM5

Step 8 — Configure the Python Serial Port

Open:

laptop_gesture_serial.py

Set the correct port:

SERIAL_PORT = "COM5"

Use the COM port assigned to your ESP32.

Step 9 — Start Real-Time Gesture Recognition

Run:

python laptop_gesture_serial.py

The webcam will start detecting the hand.

The laptop will send the extracted features to the ESP32.

The ESP32 will perform TinyML inference and control the hardware.

🔁 Complete Execution Flow

                START
                  │
                  ▼
          Open Webcam
                  │
                  ▼
        Detect Hand with
           MediaPipe
                  │
                  ▼
        Extract 21 Landmarks
                  │
                  ▼
         Normalize Features
                  │
                  ▼
          Create 63 Features
                  │
                  ▼
          Send via Serial
                  │
                  ▼
               ESP32
                  │
                  ▼
       TensorFlow Lite Micro
                  │
                  ▼
         Neural Network
                  │
                  ▼
       4-Class Prediction
                  │
                  ▼
       Confidence Filtering
                  │
             ┌────┴────┐
             │         │
           Valid     Invalid
             │         │
             ▼         ▼
       Servo + LED   Ignore

🌐 Why Use a Hybrid Architecture?

A major design decision in this project is separating hand detection from gesture classification.

Laptop

The laptop handles:

Camera
   ↓
OpenCV
   ↓
MediaPipe
   ↓
Hand Landmark Detection
   ↓
Feature Extraction

ESP32

The ESP32 handles:

63 Features
   ↓
TinyML Model
   ↓
Gesture Classification
   ↓
Servo / LED

This is useful because a full camera + hand-landmark pipeline can require considerably more computational resources than a small numerical classifier.

The ESP32 therefore receives a compact representation instead of a full image.

📉 Why 63 Features Instead of an Image?

An image contains thousands or millions of pixel values.

For example:

Image
640 × 480 pixels
        ↓
Large amount of data

The project reduces the hand representation to:

21 landmarks
      ×
3 coordinates
      =
63 values

So the embedded model only needs to process:

63 numerical inputs

This is a key TinyML concept:

Extract meaningful information first, then perform lightweight inference on the embedded device.

🧩 Technologies Used

Technology

Purpose

Python

Dataset and AI pipeline

OpenCV

Webcam capture

MediaPipe

Hand landmark detection

NumPy

Numerical operations

Pandas

Dataset processing

Scikit-learn

Dataset splitting / preprocessing

TensorFlow

Neural network training

TensorFlow Lite

Model conversion

TensorFlow Lite Micro

Embedded inference

ESP32

AI inference + hardware control

Arduino IDE

Firmware development

ESP32Servo

Servo control

USB Serial

Feature transmission

🎓 Concepts Demonstrated

Computer Vision

Webcam processing

Hand detection

Hand landmarks

Feature extraction

Machine Learning

Dataset creation

Feature engineering

Classification

Neural networks

Training and validation

Softmax probabilities

Confidence filtering

TinyML

TensorFlow Lite

TensorFlow Lite Micro

Embedded inference

Tensor arena

Lightweight neural networks

Model deployment

Embedded Systems

ESP32

GPIO

PWM / servo control

Serial communication

Real-time decision making

🔬 Understanding the Model

The neural network is deliberately small:

63 inputs
   ↓
24 neurons
   ↓
12 neurons
   ↓
4 outputs

The four outputs represent:

Output 0 → FIST
Output 1 → ONE
Output 2 → TWO
Output 3 → PALM

Softmax converts the output values into probabilities.

Example:

             Probability
FIST  ───────── 0.01
ONE   ───────── 0.05
TWO   ───────── 0.92  ← Highest
PALM  ───────── 0.02

Therefore:

Predicted Class = TWO
Confidence       = 92%

💾 Embedded Memory

TensorFlow Lite Micro uses a tensor arena as working memory for model execution.

Conceptually:

ESP32 RAM
│
├── Program variables
├── Serial buffers
├── Servo data
└── Tensor Arena
      ├── Input tensor
      ├── Intermediate tensors
      └── Output tensor

The tensor arena size must be large enough for the model and its intermediate tensors.

If the firmware reports tensor allocation errors, the tensor arena size may need to be increased.

🛠️ Troubleshooting

❌ Camera does not open

Check that the webcam is available.

Try changing:

cv2.VideoCapture(0)

to:

cv2.VideoCapture(1)

if another camera index is required.

❌ ESP32 COM port not found

Check:

Arduino IDE
→ Tools
→ Port

Then update:

SERIAL_PORT = "COM5"

with the correct port.

❌ ModuleNotFoundError

Install the missing Python package.

For example:

pip install opencv-python

For TensorFlow:

pip install tensorflow

For MediaPipe:

pip install mediapipe

For serial communication:

pip install pyserial

❌ AllocateTensors() failure

If the ESP32 reports a tensor allocation failure, increase the tensor arena size in the firmware.

For example:

constexpr int kTensorArenaSize = 30 * 1024;

can be increased if the available RAM allows it.

❌ Servo does not move

Check:

Servo signal connection

Servo power supply

ESP32 GND

External supply GND

Correct GPIO configuration

For a servo powered externally, make sure the grounds are common:

External Supply GND
        │
        ├──── ESP32 GND
        │
        └──── Servo GND

🚀 Future Improvements

The current project can be extended in many directions.

AI Improvements

Add more gesture classes

Increase dataset size

Add data augmentation

Add gesture smoothing

Add temporal gesture recognition

Quantize model to INT8

Optimize model size

Benchmark inference latency

Embedded Improvements

Add OLED display

Add multiple servo motors

Add buzzer feedback

Add Bluetooth control

Add Wi-Fi communication

Add battery operation

Robotics Improvements

Gesture-controlled robotic arm

Gesture-controlled mobile robot

Gesture-based home automation

Gesture-controlled IoT devices

Human-machine interface

Advanced Edge AI

Move hand landmark detection to an edge device

Explore ESP32-S3 camera-based inference

Use an edge accelerator

Build a completely standalone system without a laptop

🤖 Possible Applications

This architecture can be adapted to:

🦾 Robotics

Control robotic arms or mobile robots using hand gestures.

🏠 Smart Home

Use gestures to control:

Lights
Fans
Relays
Appliances

♿ Assistive Interfaces

Create alternative control interfaces using simple hand movements.

🏭 Industrial HMI

Use gestures as a contactless interface for machines and embedded systems.

🚗 Human-Machine Interaction

Build gesture-driven interfaces for vehicles or machines.

📚 Educational Value

This project is especially useful for learning Embedded AI from scratch because it connects concepts that are often taught separately.

Python
   │
   ▼
Data Collection
   │
   ▼
Computer Vision
   │
   ▼
Feature Engineering
   │
   ▼
Machine Learning
   │
   ▼
Neural Networks
   │
   ▼
TensorFlow
   │
   ▼
TensorFlow Lite
   │
   ▼
TinyML
   │
   ▼
ESP32
   │
   ▼
Real Hardware

Instead of stopping at:

"The computer predicted the gesture."

the project continues to:

"The microcontroller predicted the gesture and used that prediction to control physical hardware."

🏆 Key Takeaway

This project demonstrates a complete Embedded AI workflow:

             DATA
              ↓
        FEATURE EXTRACTION
              ↓
          ML TRAINING
              ↓
       MODEL CONVERSION
              ↓
       EMBEDDED DEPLOYMENT
              ↓
        TINYML INFERENCE
              ↓
       HARDWARE CONTROL

The final system connects:

👁️ Computer Vision
        +
🧠 Artificial Intelligence
        +
⚡ TinyML
        +
🔌 Embedded Systems
        +
⚙️ Physical Hardware

📸 Demo

Add your project images/GIF here:

![Project Demo](images/demo.gif)

Recommended demo sequence:

Show the ESP32 + servo hardware.

Show the webcam detecting the hand.

Show MediaPipe landmarks.

Show the predicted gesture.

Show the servo responding to the gesture.

🎥 Suggested Demo

A short demonstration can show:

✊ FIST
    ↓
Servo → 0°

☝️ ONE
    ↓
Servo → 30°

✌️ TWO
    ↓
Servo → 90°

✋ PALM
    ↓
Servo → 150°

A short GIF or video at the top of the README can make the project immediately understandable to visitors.

⚠️ Architecture Note

This project uses a hybrid computer-vision + TinyML architecture.

The laptop performs:

Camera Capture
      ↓
OpenCV
      ↓
MediaPipe
      ↓
21 Landmark Detection
      ↓
Feature Extraction

The ESP32 performs:

63 Feature Input
      ↓
TensorFlow Lite Micro
      ↓
Neural Network Inference
      ↓
Gesture Classification
      ↓
Actuator Control

Therefore, the ESP32 is running the gesture classification model, while the webcam and MediaPipe hand-landmark extraction run on the computer.

This distinction is important when describing the architecture accurately.

👨‍💻 Project Information

Project: ESP32 TinyML Gesture Control
Domain: Embedded AI / TinyML / Computer Vision
Platform: ESP32
Model: Lightweight Neural Network
Input: 63 normalized hand features
Output: 4 gesture classes
Actuators: Servo Motor + LED

📜 License

This project is intended for educational, research, training, and demonstration purposes.

If you reuse or modify the project, please provide appropriate attribution to the original repository.

⭐ If You Found This Project Useful

If this project helped you learn about:

Embedded AI

TinyML

TensorFlow Lite

ESP32

Computer Vision

Gesture Recognition

consider giving the repository a ⭐ Star.

<p align="center">

🧠 Learn AI

⚡ Deploy AI

🤖 Make Hardware Intelligent

</p>
