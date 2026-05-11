# Introduction to computer vision concepts

Learning objectives

- Identify different types of computer vision tasks
- Describe how filters are used in image analysis
- Describe the main features of a convolutional neural network (CNN)
- Describe the main features of a vision transformer (ViT)
- Describe how generative AI can be used to create images

# Understanding Image Processing

## Overview
Image processing is the foundational stage of computer vision. It involves manipulating and analyzing digital images so AI systems can extract meaningful information.

Computer vision systems typically:
1. acquire an image
2. preprocess it
3. extract features
4. interpret content using AI models

Image processing improves image quality and prepares visual data for machine learning models.

---

## Digital Images

### Pixels
Digital images consist of pixels arranged in a grid.

Each pixel stores color or intensity values.

Example:
- grayscale image → single intensity value
- RGB image → red, green, blue channel values

Higher resolution means:
- more pixels
- greater detail
- larger storage and processing requirements

---

## Image Channels

| Image Type | Description |
|---|---|
| Grayscale | Single intensity channel |
| RGB | Red, Green, Blue channels |
| RGBA | RGB plus transparency |
| Binary image | Pixels represented as black or white |

---

## Common Image Processing Operations

### 1. Resizing
Changes image dimensions.

Use cases:
- normalize dataset sizes
- reduce computational cost
- adapt images for model input requirements

---

### 2. Cropping
Removes irrelevant image regions.

Benefits:
- focus on target objects
- reduce noise
- improve model accuracy

---

### 3. Rotation and Flipping
Creates modified image versions.

Commonly used for:
- data augmentation
- improving model generalization

---

### 4. Noise Reduction
Removes unwanted distortions from images.

Techniques:
- smoothing
- blurring
- filtering

Improves feature extraction reliability.

---

### 5. Edge Detection
Identifies object boundaries and shape transitions.

Used in:
- object detection
- segmentation
- feature extraction

Common algorithms:
- Sobel
- Canny

---

## Feature Extraction

### Purpose
Transforms raw pixel data into meaningful representations.

Examples of extracted features:
- edges
- corners
- textures
- shapes
- patterns

Traditional computer vision relied heavily on manual feature engineering before deep learning automated the process.

---

## Histograms in Image Processing

### Definition
A histogram represents pixel intensity distribution.

Applications:
- contrast analysis
- brightness adjustment
- thresholding

---

## Thresholding

### Concept
Separates objects from background based on intensity values.

Example:
- convert grayscale image into binary image

Used in:
- OCR preprocessing
- segmentation
- industrial inspection systems

---

## Image Segmentation

### Purpose
Partitions an image into meaningful regions or objects.

Examples:
- isolate tumors in medical imaging
- identify roads in satellite imagery
- separate foreground from background

Segmentation is more detailed than image classification because it analyzes pixel-level regions.

---

## Object Detection

### Difference from Classification

| Task | Output |
|---|---|
| Image classification | What is in the image |
| Object detection | What objects exist and where they are |

Object detection returns:
- class labels
- bounding boxes
- confidence scores

---

## Optical Character Recognition (OCR)

### Purpose
Extracts text from images.

Common uses:
- document digitization
- invoice processing
- license plate recognition

---

## Facial Detection vs Facial Recognition

| Concept | Meaning |
|---|---|
| Face detection | Identifies presence/location of faces |
| Face recognition | Identifies specific individuals |

Important AI-900 distinction:
- detection ≠ identification

---

## Image Processing Pipeline

| Stage | Purpose |
|---|---|
| Acquisition | Capture image |
| Preprocessing | Improve image quality |
| Feature extraction | Detect important characteristics |
| Analysis | Apply AI models |
| Interpretation | Produce predictions or insights |

---

## Traditional Computer Vision vs Deep Learning

| Traditional CV | Deep Learning CV |
|---|---|
| Manual feature engineering | Automatic feature learning |
| Rule-based methods | Data-driven learning |
| Limited scalability | Highly scalable |
| Smaller datasets sufficient | Often requires larger datasets |

---

## Azure AI Vision Context

Azure AI Vision supports:
- OCR
- image tagging
- object detection
- facial analysis
- image captioning

Benefits:
- pretrained APIs
- cloud scalability
- minimal ML expertise required

---
---
# Introduction to Computer Vision Models

## Overview
Computer vision models enable AI systems to interpret and analyze visual data such as images and video.

- Convolutional Neural Networks (CNNs)
- Vision Transformers (ViTs)
- Multimodal models

---

## Convolutional Neural Networks (CNNs)

### Purpose
CNNs are specialized neural networks designed for:
- image classification
- object detection
- facial recognition
- segmentation

---

## Core CNN Concepts

### Convolution Layers
CNNs use filters to detect:
- edges
- corners
- textures
- shapes

### Pooling Layers
Pooling reduces dimensionality while preserving important features.

### Fully Connected Layers
Final layers perform classification using extracted features.

---

## Vision Transformers (ViTs)

### Concept
ViTs:
- split images into patches
- process patches using self-attention
- model global image relationships

---

## CNN vs Vision Transformer

| Feature | CNN | Vision Transformer |
|---|---|---|
| Mechanism | Convolutions | Self-attention |
| Focus | Local features | Global context |
| Data efficiency | Better with small datasets | Better with large datasets |

---

## Multimodal Models

### Examples
- image captioning
- visual question answering
- text-to-image generation

---

## Transfer Learning

Pretrained models are fine-tuned for new tasks.

Benefits:
- reduced training cost
- less labeled data required
- faster deployment

---

## Key Takeaways

- CNNs extract hierarchical visual features.
- ViTs use self-attention for global context understanding.
- Multimodal models combine image and language reasoning.

---
---
# Modern Vision Models

## Overview
Modern computer vision increasingly uses:
- foundation models
- multimodal AI
- generative AI

These models generalize across many tasks.

---

## Foundation Models

### Characteristics
- pretrained on massive datasets
- reusable across tasks
- support fine-tuning

---

## Multimodal AI

### Combines
- images
- text
- audio
- video

Examples:
- image captioning
- semantic image search
- VQA

---

## Diffusion Models

### Process
1. Start with noise
2. Iteratively denoise
3. Produce final image

Advantages:
- high-quality outputs
- strong prompt alignment

---

## Zero-Shot Learning

Models perform tasks without task-specific retraining.

Enabled by:
- multimodal pretraining
- embedding spaces

---

## Responsible AI

### Risks
- bias
- hallucinations
- privacy concerns
- harmful content generation

### Mitigations
- filtering
- governance
- human oversight

---

## Key Takeaways

- Foundation models support generalized vision tasks.
- Diffusion models power modern image generation.
- Responsible AI is essential in vision systems.

---
---
# Generating Images with AI

## Overview
Generative AI models can create images from natural language prompts using multimodal foundation models.

---

## Text-to-Image Generation

### Workflow

| Step | Description |
|---|---|
| Prompt input | User provides description |
| Semantic interpretation | Model extracts meaning |
| Image synthesis | AI generates image |
| Refinement | Iterative improvement |

---

## Prompt Engineering

### Important Elements
- subject
- artistic style
- lighting
- environment
- mood

Detailed prompts improve output quality.

---

## Image Editing

Modern systems support:
- inpainting
- outpainting
- image transformations
- object removal

---

## Responsible AI

### Risks
- deepfakes
- misinformation
- harmful content
- copyright concerns

### Mitigations
- moderation
- filtering
- governance

---

## Key Takeaways

- Diffusion models drive modern image generation.
- Prompt engineering strongly affects outputs.
- Generative AI systems require governance and safety controls.

---

## Official References

- https://learn.microsoft.com/en-us/training/modules/introduction-computer-vision/3-understand-image-processing?pivots=text
- https://learn.microsoft.com/en-us/training/modules/introduction-computer-vision/4-computer-vision-models?pivots=text
- https://learn.microsoft.com/en-us/training/modules/introduction-computer-vision/5-modern-vision-models?pivots=text
- https://learn.microsoft.com/en-us/training/modules/introduction-computer-vision/5a-generate-images?pivots=text
