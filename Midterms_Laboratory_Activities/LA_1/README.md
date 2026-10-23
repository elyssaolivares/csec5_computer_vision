# Laboratory Activity 1: Coin Detection
**Computer Vision: Convolution, Spatial Filtering, and Edge Detection**

---

## 1. Objective

Use convolution, spatial filtering, and edge detection to process images of Philippine coins and produce a clean edge-detected result in which the coins forming **₱21.10** can be clearly identified.

---

## 2. Selected Coins

The combined image (`coins.png`) contains four coins from the dataset:

| # | Coin | Year | Value |
|---|------|------|-------|
| 1 | 10 Piso (bi-metallic) | 2014 | ₱10.00 |
| 2 | 10 Piso (bi-metallic) | 2004 | ₱10.00 |
| 3 | 1 Piso (Jose Rizal) | 2010 | ₱1.00 |
| 4 | 10 Sentimo | 2009 | ₱0.10 |

**Total: ₱10.00 + ₱10.00 + ₱1.00 + ₱0.10 = ₱21.10** ✅

---

## 3. Methodology

### Step 1: Load the Image
The image is loaded with OpenCV. If the PNG has a transparent channel, it is composited onto a white background so transparent pixels do not turn into black "background".

### Step 2: Pre-Processing
1. **Grayscale conversion** reduces the image to a single intensity channel.
2. **Noise reduction** with two filters:
   - Median filter (size 5), which removes salt-and-pepper noise.
   - Gaussian blur (5×5), which smooths the image evenly. This result is used for the next step.

### Step 3: Convolution (Sharpening)
A 3×3 sharpening kernel is applied to the Gaussian-blurred image using `cv2.filter2D`.

```
[ 0, -1,  0]
[-1,  5, -1]
[ 0, -1,  0]
```

### Step 4: Edge Detection (Canny)
`cv2.Canny` is run on the sharpened image. The thresholds are set automatically from the median brightness of the **coin pixels only** (low = 0.5× median, high = 1.2× median).

### Step 5: Cleaning the Result
- A silhouette mask isolates the coins.
- Blobs that are too small or not round enough are discarded.
- An ellipse is fitted to each coin to draw a clean rim.
- Canny edges are kept only inside the coins.
- Small disconnected specks are removed.
- The rim (green) and inner details (cyan) are drawn in different colors.

---

## 4. Output Images

| Required Output | Notebook Result |
|-----------------|-----------------|
| 1. Original combined coin image | *Original Combined Image* |
| 2. Grayscale / filtered image | *Grayscale, Median Filter, Gaussian Blur* |
| 3. Convolution result | *Sharpened Image (After Convolution)* |
| 4. Final edge-detected output | *Cleaned Edges* and *Detected Coins* overlay |

> Insert the exported figures from the notebook here, for example:
> `![Original](original.png)` `![Grayscale](gray.png)` `![Sharpened](sharpened.png)` `![Final](final.png)`

---

## 5. Short Explanation

### Kernel Used
A 3×3 sharpening kernel. The center value of **5** boosts the current pixel, and the **−1** neighbors subtract the surrounding brightness. This increases local contrast and emphasizes edges. A 7×7 elliptical structuring element is also used for the morphological operations on the silhouette mask.

### Convolution Process
The image is converted to grayscale and smoothed with the median and Gaussian filters. The sharpening kernel then slides over the Gaussian-blurred image with `cv2.filter2D`. At each position it multiplies the neighboring pixels by the kernel values and sums them. The result is an image with higher-contrast edges and finer detail.

### Edge Detection Method
**Canny edge detection** is applied to the sharpened image. Using adaptive thresholds from the coin pixels, instead of fixed values, keeps the white background from skewing the result and lets the method adapt to different photos.

### How the Result Was Improved
1. **Silhouette mask:** thresholding each pixel's color distance from white keeps the bright silver rims that grayscale thresholding cut into.
2. **Shape filtering:** small or non-round blobs are removed, and an ellipse is fitted to each coin, which also handles tilted coins.
3. **Clean rims:** the fitted ellipse gives a continuous outline for each coin instead of relying on Canny for the outer boundary.
4. **Detail edges:** Canny edges are kept only inside the coins, and connected-component filtering removes small specks. Portraits, numerals, and dates stay visible as thin lines.
5. **Visualization:** the rim and detail edges are color-coded and overlaid on the original photo, with each coin's center, diameter, and roundness reported.

---

## 6. Conclusion

Sharpening convolution strengthened the coin boundaries and markings, and Canny edge detection traced them. Silhouette-based cleanup then removed background noise. The final output shows all four coins with clear circular edges and visible markings (portraits, numerals, and years), so the total of **₱21.10** can be verified visually.
