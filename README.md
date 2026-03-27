# Image Stitching with OpenCV

A clean, notebook-based computer vision project that stitches two overlapping images into a single panorama using classical feature matching and geometric transformation techniques.

## Overview

This project demonstrates a full panorama stitching workflow implemented in **OpenCV** inside a Jupyter notebook. The pipeline loads a left and right image, detects local features with **SIFT**, matches descriptors with **BFMatcher**, estimates a robust projective transform using **RANSAC**, warps one image into the other image's coordinate space, and trims the final panorama to remove empty borders.

The repository is a good fit for:
- computer vision coursework,
- OpenCV practice,
- homography and panorama demos,
- portfolio projects that show end-to-end image alignment.

## Pipeline

The notebook follows this sequence:

1. **Load input images**  
   Reads `left.jpg` and `right.jpg`, then converts them to grayscale for feature extraction.

2. **Detect keypoints and descriptors**  
   Uses `cv2.xfeatures2d.SIFT_create()` to extract scale-invariant keypoints and descriptors.

3. **Match descriptors**  
   Uses `cv2.BFMatcher()` with `knnMatch(..., k=2)` to retrieve the two nearest matches per descriptor.

4. **Filter matches**  
   Applies Lowe-style ratio filtering to keep stronger correspondences.

5. **Estimate homography**  
   Uses `cv2.findHomography(..., cv2.RANSAC, 5.0)` to compute the perspective transformation between the two views.

6. **Warp and stitch**  
   Uses `cv2.warpPerspective(...)` to project one image into panorama space, then copies the second image into the canvas.

7. **Trim borders**  
   Removes black borders from the stitched output with a recursive trimming function.

## Repository Contents

```text
.
├── Image-stiching.ipynb   # Main notebook (original filename kept as-is)
├── left.jpg               # Input image A (expected)
├── right.jpg              # Input image B (expected)
└── README.md              # Project documentation
```

## Requirements

- Python 3.7+
- Jupyter Notebook or JupyterLab
- OpenCV with contrib modules
- NumPy
- Matplotlib

Install dependencies with:

```bash
pip install opencv-contrib-python numpy matplotlib jupyter
```

## How to Run

1. Clone the repository.
2. Place two overlapping images in the project folder named:
   - `left.jpg`
   - `right.jpg`
3. Start Jupyter:

```bash
jupyter notebook
```

4. Open `Image-stiching.ipynb`.
5. Run the notebook cells from top to bottom.

## Generated Outputs

The notebook creates these files during execution:

- `keypoints.jpg` — detected keypoints visualization.
- `warped.jpg` — perspective-warped image.
- `output.jpg` — initial stitched panorama before trimming.
- `finaloutput.jpg` — trimmed final panorama.

## Example Code Sections

### SIFT feature extraction

```python
sift = cv2.xfeatures2d.SIFT_create()
kp1, des1 = sift.detectAndCompute(img1, None)
kp2, des2 = sift.detectAndCompute(img2, None)
```

### Descriptor matching

```python
match = cv2.BFMatcher()
matches = match.knnMatch(des1, des2, k=2)
```

### Homography estimation

```python
H, masked = cv2.findHomography(src, dst, cv2.RANSAC, 5.0)
```

### Perspective warping

```python
dst = cv2.warpPerspective(img_, H, (img.shape[1] + img_.shape[1], img.shape[0]))
```

## Notes and Limitations

- The notebook depends on **SIFT**, which requires the OpenCV contrib package.
- Stitching quality depends heavily on overlap, texture, and lighting consistency between the two images.
- The current implementation is designed for **two images only**.
- Blending is minimal; visible seams can still appear in difficult cases.

## Improvement Ideas

Possible upgrades for a stronger production version:

- Add automatic canvas sizing and smarter cropping.
- Support more than two images.

### Poor stitching result

Check that:
- the images have enough overlap,
- the scene is mostly static,
- the images were taken from roughly the same optical center,
- lighting changes are not too strong.

