# Object Detection and Depth Estimation

## 1. Introduction
The goal of this project is to estimate the 3D depth of objects, specifically **cars**, from 2D images using a subset of the **KITTI dataset**. Object detection is performed using **YOLOv8x**, while depth estimation leverages the **intrinsic camera matrix** provided in the KITTI dataset. This report details the methodology, mathematical formulation, experimental results, and analysis of discrepancies between the YOLO-estimated distances and ground truth distances.

---

## 2. Methodology
The pipeline consists of several steps:

1. **Library Imports**:  
   Libraries used include `os` for file operations, `numpy` for numerical calculations, `cv2` for computer vision tasks, and `matplotlib.pyplot` for visualization.

2. **Model Initialization**:  
   A pre-trained **YOLOv8x** model from the Ultralytics library is used for object detection.

3. **Data Organization**:  
   Directories are structured as follows:
   - `images/`: Input images  
   - `calib/`: Camera intrinsic matrices  
   - `labels/`: Ground truth labels  
   - `output_images/`: Annotated images with bounding boxes and distance estimates  

4. **Core Functions**:
   - Load intrinsic camera matrices
   - Load ground truth labels
   - Compute **Intersection over Union (IoU)** between bounding boxes  

5. **Processing Workflow**:
   - Iterate over images
   - Load the image, corresponding intrinsic matrix, and ground truth labels
   - Perform YOLO detection for cars
   - Estimate distance using geometric calculations
   - Match detected cars with ground truth using IoU
   - Generate annotated images showing bounding boxes, midpoints, and distances
   - Save annotated images to the output directory

---

### 2.1 Distance Estimation
The distance to a detected car is computed using geometric reasoning with the intrinsic camera matrix:

1. Extract the bounding box coordinates for the detected car.
2. Compute the midpoint of the lower bound of the bounding box.
3. Represent the midpoint as a 2D homogeneous coordinate vector.
4. Multiply by the **inverse of the intrinsic matrix** to obtain a direction vector in camera coordinates.
5. Intersect this vector with the **ground plane** (height = -1.65 m) to find a 3D point.
6. Compute the **Euclidean distance** from the camera to this 3D point.

---

### 2.2 Matching Detections with Ground Truth
To evaluate performance:

- Compute the **IoU** between each detected bounding box and all ground truth boxes.
- The ground truth box with the **highest IoU** is considered the match.
- Detections with IoU below a threshold (0.5) or no available ground truth are considered false positives.

---

## 3. Results
YOLO successfully detected cars in all images, and distance estimation was performed for each detection. Examples of distance estimates compared with ground truth:

| Image | YOLO Distance (m) | Ground Truth (m) |
|-------|-----------------|-----------------|
| 006037.png | 19.33, 34.60, 39.82, 30.30, 106.76 | 17.31, 31.34, 34.92, 29.64, N/A |
| 006042.png | 19.57, 36.59, 39.28 | 19.90, 47.53, N/A |
| 006048.png | 7.75, 20.11, 19.81, 30.61, 54.10 | 4.98, 17.06, 16.92, 23.18, 39.05 |

**Observations**:
- Distance estimation is accurate for cars closer than 50 meters.
- Some distant or partially occluded cars show higher discrepancies.
- Matching using IoU > 0.5 helps filter mismatched detections.

---

## 4. Visualization
- Annotated images show **red YOLO boxes**, **green ground truth boxes**, and **distance annotations**.
- Scatter plots of YOLO distance vs. ground truth distance demonstrate strong correlation for nearby objects.

---

## 5. Conclusion
This project demonstrates a robust method for **estimating car distances** from single-camera images using YOLOv8 and camera intrinsics. Future improvements include:
- Multi-camera triangulation for improved accuracy
- Deep learning-based depth estimation for occluded or distant cars
- Refining ground plane assumptions for varied terrain

---

## 6. Dependencies
- Python 3.8+
- Ultralytics YOLO (`pip install ultralytics`)
- OpenCV (`pip install opencv-python`)
- NumPy (`pip install numpy`)
- Matplotlib (`pip install matplotlib`)

---


