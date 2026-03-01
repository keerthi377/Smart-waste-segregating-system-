# Smart-waste-segregating-system-
Smart dustbin that automatically segregates waste into 5 different types of waste namely metal, wet, glass, plastic and paper
## Project Overview

AI-Based Smart Waste Segregation System that automatically classifies and sorts waste using sensors and deep learning model.

The system segregates waste into 5 different categories namely metal, wet, glass, plastic & paper and directs it into appropriate bins using an automated servo-based mechanism.

## Objectives

* Automate waste segregation at source<br>
* Reduce manual sorting effort<br>
* Improve recycling efficiency<br>
* Demonstrate edge AI deployment using embedded systems<br>
* Combine sensors and deep learning for robust classification<br>


## Waste Classification Strategy

The smart waste segregation system uses a hybrid classification strategy in which metal waste is detected using an inductive proximity sensor, wet waste is identified using a capacitive moisture sensor, and the remaining dry waste categories — plastic, paper, and glass — are classified using a MobileNetV2 deep learning model. This combined approach improves reliability because rusted or dirty metal objects may confuse vision-based models and are therefore detected physically using a sensor, wet waste is accurately recognized through moisture measurement, and only the remaining dry waste is processed using artificial intelligence for classification.


## **System Architecture**

The smart waste segregation system is centered around a Raspberry Pi 4B, which acts as the main processing unit. A Logitech webcam mounted above the drop zone captures images of the waste item placed inside the bin. The Raspberry Pi simultaneously receives inputs from two sensors: an inductive metal sensor connected through GPIO pins for detecting metallic waste, and a capacitive moisture sensor connected via an MCP3008 ADC using SPI communication for identifying wet waste.

If neither metal nor wet waste is detected, the captured image is processed using a MobileNetV2 deep learning model running on the Raspberry Pi to classify the object as plastic, paper, or glass. Based on sensor readings and model prediction, a decision logic engine determines the final waste category.

The system uses **two servo motors** for mechanical operation:

* The **top servo motor** controls a flap mechanism that opens to allow waste entry and closes during classification to hold the object in place.
* The **base servo motor** rotates the dustbin to align the correct compartment with the flap, ensuring the waste is dropped into the appropriate bin.

---

## Hardware Components

* Raspberry Pi 4B/ESP32<br>
* Sensor interfacing<br>
* Deep learning inference<br>
* Servo control<br>
* Inductive metal proximity sensor<br>
* Capacitive soil moisture sensor (v1.2)<br>
* MCP3008 Analog-to-Digital Converter<br>
* Logitech USB Webcam <br>
* 180° positional servo motor for top flap rotation<br>
* 180° positional servo motor for base bin rotation<br>

## Sorting Mechanism

A single **180° positional servo motor** located at base rotates to predefined angles:

0°   → Plastic<br>
45°  → Paper<br>
90°  → Glass<br>
135° → Wet Waste<br>
160° → Metal<br>

## Deep Learning Model

**MobileNetV2 (Transfer Learning)**

* Lightweight CNN<br>
* Optimized for edge devices<br>
* Fast inference on Raspberry Pi<br>
* Good performance with small datasets<br>

## Dataset

The dataset was sourced from **Kaggle**, containing approximately 500 images each of glass, paper, and plastic (≈1500 images total). The dataset was then augmented to around 1000 images per class and split into **80% training**, **15% testing**, and **5% validation** sets.

## Transfer Learning Approach

Instead of training the model from scratch,  pretrained ImageNet weights  were used with  MobileNetV2 , where the base layers were frozen and only a custom classification head was trained. Since the model already understands fundamental visual features such as edges, textures, shapes, and materials, it was fine-tuned for waste classification using 25 training epochs.

## Model Architecture & Training Configuration

The model uses MobileNetV2 as a pretrained backbone followed by Global Average Pooling, a Dense layer (128 neurons, ReLU activation), Dropout (0.3) for regularization, and a final Dense layer with Softmax activation for three-class classification.

Training parameters: input size 224×224, Adam optimizer, learning rate 0.0001, batch size 16, trained for 25 epochs using categorical crossentropy loss.

## Advantages

* Faster inference<br>
* Reduced memory usage<br>
* Suitable for Raspberry Pi edge deployment<br>


## Raspberry Pi Responsibilities

1. Read metal sensor (GPIO)<br>
2. Read moisture values via MCP3008 (SPI)<br>
3. Capture image from webcam<br>
4. Run TFLite inference<br>
5. Apply decision logic<br>
6. Control servo motor<br>

## System Workflow

User drops waste<br>
        ↓		<br>      					 
Metal sensor check/Moisture check (yes)-> Flap opens -> Waste Sorted to appropriate class<br>
        ↓(no)<br>
Camera captures image<br>
        ↓<br>
DL classification <br>
        ↓  <br>      
   Flap opens<br>
        ↓<br>
Waste sorted appropriate class<br>

## ⚙️ Troubleshooting

After converting MobileNetV2 model to TensorFlow Lite and tested on the Raspberry Pi with real-time images the model was able to classify correctly. However, issues arose when integrating servo motor control using the gpiozero library. Removing pyenv configurations allowed the servo code to run, but caused the ML model execution to fail due to environment conflicts.

The main problem was Python version incompatibility. Raspberry Pi OS Trixie uses Python 3.13 , while official tflite-runtime wheels are available only for Python 3.9–3.11.

### Solution
* Created a Python 3.11 virtual environment using pyenv.<br>
* Installed required libraries (numpy, opencv-python, gpiozero).<br>
* Downloaded a compatible prebuilt wheel (tflite_runtime-2.16.1) from the PINTO0309/tensorflow-bin repository.<br>
* Installed it manually on the Pi.<br>
* Replaced TensorFlow import with:<br>
import tflite_runtime.interpreter as tflite<br>

This resolved compatibility issues, allowing both ML inference and servo motor control to work correctly in the same environment.


## Future Improvements

* Add more waste categories<br>
* Conveyor-based automation<br>
* Cloud monitoring dashboard<br>
* IoT data analytics<br>


## Technologies Used

* Python
* TensorFlow / TensorFlow Lite
* OpenCV
* Raspberry Pi OS
* SPI & GPIO Interfaces
* Embedded Systems
* Deep Learning (CNN)
