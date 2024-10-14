# LSTM-Based Intrusion Detection System for CAN Bus Networks

**TechTogether Expo Fall 2024**  
**msg Plaut Hungary Kft.**  
**Team: ELTE StudentTechLab**  
**Date: October 6, 2024**

## Introduction

This project outlines the development of an LSTM-based Intrusion Detection System (IDS) for CAN Bus communications. The primary goal is to detect malicious messages (fuzzing attacks) in the CAN network using deep learning techniques.

## Preprocessing the CAN Message Data Field

The preprocessing of the CAN message `data field` is crucial for extracting relevant features to train machine learning models for intrusion detection. The `data field` in a CAN message contains up to 8 bytes of hexadecimal data, which needs fine-grained processing to capture potential attack vectors.

### 1. Splitting the Data Field into Bytes

- The `data field` is split into individual byte columns (D0 to D7) to enable granular analysis.
- Missing or invalid fields (e.g., NaN or floats) are replaced by 8 zero bytes.
- Data fields shorter than 8 bytes are padded to 16 hexadecimal characters.
- Each 2-character hexadecimal chunk is converted into its integer representation for machine learning compatibility.

### 2. Rationale for Using D0 to D7

By splitting the `data field` into individual bytes, the model can capture fine-grained anomalies in specific bytes. This is particularly relevant for detecting fuzzing and spoofing attacks. The following 10 features are used for training the model:

- `timestamp`
- `arbitration id`
- Byte columns: `D0` to `D7`

## Model Development Process

### 1. Model Layers

The model consists of the following layers:

- **LSTM Layer 1:** 64 units, returning sequences, batch normalization, dropout (0.3)
- **LSTM Layer 2:** 32 units, batch normalization, dropout (0.3)
- **Fully Connected Layer:** 16 units, ReLU activation
- **Output Layer:** 1 unit, sigmoid activation for binary classification (attack or non-attack)

### 2. Explanation of Layers

- The first LSTM layer captures temporal patterns in CAN messages with 64 units.
- The second LSTM layer refines these patterns using 32 units.
- A dense layer condenses the features into a smaller representation using ReLU activation.
- The output layer produces a probability score using sigmoid activation to classify the sequence.

### 3. Model Summary

| Layer Type            | Output Shape | Parameters |
|-----------------------|--------------|------------|
| LSTM Layer 1          | (None, 10, 64) | 19,200    |
| Batch Normalization    | (None, 10, 64) | 256       |
| Dropout               | (None, 10, 64) | 0         |
| LSTM Layer 2          | (None, 32)     | 12,416    |
| Batch Normalization    | (None, 32)     | 128       |
| Dropout               | (None, 32)     | 0         |
| Dense Layer           | (None, 16)     | 528       |
| Dropout               | (None, 16)     | 0         |
| Output Layer          | (None, 1)      | 17        |

**Total Parameters:** 32,545 (127.13 KB)  
**Trainable Parameters:** 32,353 (126.38 KB)  
**Non-Trainable Parameters:** 192 (768.00 B)

## Loss Function

The binary cross-entropy loss function is used, as this is a binary classification problem distinguishing between benign and malicious (fuzzing) messages. The Nadam optimizer is employed for adaptive learning rate capabilities, aiding in faster convergence during training.

## Hyperparameter Tuning

- **Sequence Length:** Set to 10 (with 20, 40, and 50 also tested).
- **LSTM Layer Units:** Tuned to 64 and 32 units for the two layers.
- **Dropout Rate:** Set to 0.2 (0.3 tested, but yielded worse results).
- **Learning Rate:** 0.0001 for stable convergence.

### Model Evaluation Results

- **Test Accuracy:** 95.87%
- **Loss:** 0.2560
- **Precision:** 0.7809
- **Recall:** 0.7652
- **F1 Score:** 0.7729

These results were obtained after training for four epochs on limited resources (Google Colab). Additional training is expected to further improve performance.

## Conclusion

This LSTM-based model demonstrates robust performance for detecting fuzzing attacks on CAN Bus networks, with fine-tuned preprocessing and hyperparameters. The model generalizes well across unseen test data.

