<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

<style>
  body {
    background-color: #FFFAF0;
  }
  .image-container {
    background-color: #E0FFFF;
    padding: 10px;
    border-radius: 5px;
  }
  img {
    display: block;
    margin-left: auto;
    margin-right: auto;
  }
</style>

# CS 180 Project 2: Fun with Filters and Frequencies

## Part 1: Fun with Filters
In this part, we will build intuitions about 2D convolutions and filtering.

### Part 1.1: Finite Difference Operator

We will begin by using the humble finite difference as our filter in the x and y directions.

$$D_x = \begin{bmatrix}1 & -1\end{bmatrix}, D_y = \begin{bmatrix}1 \\-1\end{bmatrix}$$

The gradient magnitude calculation involves the following theoretical steps:

1. Image Representation: The image is represented as a 2D array of pixel intensities.

2. Derivative Operators: Two derivative operators are defined:

3. Horizontal Derivative Operator (D_x): Detects changes in the horizontal direction.
Vertical Derivative Operator (D_y): Detects changes in the vertical direction.
Convolution: The image is convolved with the horizontal and vertical derivative operators to compute the derivatives in the x and y directions. This step highlights the intensity changes in the respective directions.

4. Gradient Magnitude Calculation: The gradient magnitude at each pixel is computed using the formula: 
    
    $$
    \text{gradient_magnitude} = \sqrt{\text{derivative_x}^2 + \text{derivative_y}^2}
    $$

    This combines the horizontal and vertical derivatives to give the overall edge strength at each pixel.

5. Edge Detection: A threshold is applied to the gradient magnitude to create a binary edge image, where pixels with gradient magnitudes above the threshold are considered edges.

The Finite Difference Operator estimates image gradients by computing horizontal and vertical derivatives, which reveal intensity changes, indicating edges. It uses simple kernels to calculate these derivatives through convolution, and the gradient magnitude is derived by combining both directions. However, this method is sensitive to noise, as it directly processes raw pixel values. Edges are detected by thresholding the gradient magnitudes, making it a straightforward but noise-prone approach for edge detection. After multiple adjustments to the threshold and implementations, I found that the best result was achieved with a threshold of 60.

![Part 1.1 Finite Difference Operator](project2_data/1/1.png)

### Part 1.2: Derivative of Gaussian (DoG) Filter
Firstly, create a blurred version of the original image by convolving with a gaussian and repeat the procedure in the previous part (using `cv2.getGaussianKernel()` to create a 1D gaussian and then taking an outer product with its transpose to get a 2D gaussian kernel).

The processed image clearly contains less noise and has more distinct edges.

![Part 1.2 Derivative of Gaussian (DoG) Filter](project2_data/1/2.png)

Then, do the same thing with a single convolution instead of two by creating a derivative of gaussian filters (DoG Filter)

![Part 1.2 Derivative of Gaussian (DoG) Filter](project2_data/1/3.png)
![Part 1.2 Derivative of Gaussian (DoG) Filter](project2_data/1/4.png)

These two methods get the same result.

![Part 1.2 Derivative of Gaussian (DoG) Filter](project2_data/1/5.png)


## Part 2: Fun with Frequencies!

### Part 2.1: Image "Sharpening"
The principle of sharpening an image by subtracting a Gaussian-blurred version from the original image is based on enhancing edges and fine details. In this process, the original image is first blurred using a Gaussian filter to reduce noise and smooth out the details. By subtracting this blurred image from the original, the high-frequency components (representing edges and details) are amplified. Steps:

1. **Image Normalization**: The input image is read and normalized to have pixel values in the range \([0, 1]\).

2. **Gaussian Blurring**: A Gaussian blur is applied to the normalized image. This involves convolving the image with a Gaussian kernel, which smooths the image by averaging pixel values with their neighbors, weighted by a Gaussian function. Mathematically, the Gaussian function is given by:

   $$
   G(x, y) = \frac{1}{2\pi\sigma^2} e^{-\frac{x^2 + y^2}{2\sigma^2}}
   $$

3. **High-Frequency Component Extraction**: The high-frequency components of the image are extracted by subtracting the blurred image from the original image. This step isolates the details and edges in the image. If \(I\) is the original image and \(B\) is the blurred image, the high-frequency component \(H\) is:

   $$
   H = I - B
   $$

4. **Sharpening**: The high-frequency components are scaled by a factor (\(\alpha\)) and added back to the original image. This enhances the details and edges, making the image appear sharper. The sharpened image \(S\) is given by:

   $$
   S = I + \alpha H = I + \alpha (I - B)
   $$

![Part 2.1: Image "Sharpening"](project2_data/taj.png)
![Part 2.1: Image "Sharpening"](project2_data/2_1/taj2.png)
![Part 2.1: Image "Sharpening"](project2_data/2_1/tower.png)

For a character image from GTA5, I first applied a blur and then sharpening, and found that the sharpening effect was significant.

<div style="display: flex; justify-content: space-around;">
    <div style="text-align: center;">
        <img src="project2_data/2_1/GTA5.jpg" alt="GTA5"/>
        <p>GTA5</p>
    </div>
    <div style="text-align: center;">
        <img src="project2_data/2_1/GTA5_blurred.jpg" alt="GTA5_blurred"/>
        <p>GTA5_blurred</p>
    </div>
    <div style="text-align: center;">
        <img src="project2_data/2_1/GTA5_sharpened.jpg" alt="GTA5_sharpened"/>
        <p>GTA5_sharpened</p>
    </div>
</div>

<div style="display: flex; justify-content: space-around;">
    <div style="text-align: center;">
        <img src="project2_data/2_1/GreatWall.jpg" alt="GreatWall"/>
        <p>GreatWall</p>
    </div>
    <div style="text-align: center;">
        <img src="project2_data/2_1/GreatWall_sharpened.jpg" alt="GreatWall_sharpened"/>
        <p>GreatWall_sharpened</p>
    </div>
</div>

<div style="display: flex; justify-content: space-around;">
    <div style="text-align: center;">
        <img src="project2_data/2_1/Sea.jpg" alt="Sea"/>
        <p>Sea</p>
    </div>
    <div style="text-align: center;">
        <img src="project2_data/2_1/Sea_sharpened.jpg" alt="Sea_sharpened"/>
        <p>Sea_sharpened</p>
    </div>
</div>

### Part 2.2: Hybrid Images
1. **Cropping and Resizing Images**:
   - Crop and resize two images to ensure they have the same dimensions. This step is essential for allowing effective blending in the later stages.

2. **Defining the Gaussian Function**:
   - Use the Gaussian function to model the filtering process. This function helps in determining how much each pixel contributes to the final image based on its distance from the center of the filter.

3. **Creating Filter Matrices**:
   - Generate filter matrices for both low-pass and high-pass filtering. For low-pass filtering, I applied the Gaussian function directly to retain smooth features. In contrast, for high-pass filtering, I inverted the Gaussian to enhance the image's edges and details.

4. **Applying Frequency Domain Filtering**:
   - Transforme the color channels of the images into the frequency domain using Fast Fourier Transform (FFT). This allows to apply the filter matrices effectively. After filtering, transform the images back to the spatial domain to retrieve the modified pixel values.

**Bells & Whistles:** Using color to enhance the effect, I found that both high-frequency and low-frequency images work best when their colors are preserved, so I chose to use color images for my work.

**About bad results:** My initial attempt resulted in an incorrect outcome because I did not consider that overlaying two images might cause pixel values to exceed the range of 255, leading to distortion in one of the RGB channels, manifesting as bizarre color artifacts. This was due to the lack of a normalization step.

![Part 2.2: Hybrid Images](project2_data/2_2/bad_result.png)

After fixing this bug, I got the perfect result:

![Part 2.2: Hybrid Images](project2_data/2_2/1.png)

I like the game Elden Ring, so I decided to make a funny version of Melina.

![Part 2.2: Hybrid Images](project2_data/2_2/2.png)

There are also some funny ideas.

![Part 2.2: Hybrid Images](project2_data/2_2/3.png)
![Part 2.2: Hybrid Images](project2_data/2_2/4.png)


## Multi-resolution Blending and the Oraple journey

### Part 2.3: Gaussian and Laplacian Stacks

<div style="text-align: center;">
    <img src="project2_data/2_3/mask.png" alt="Part 2.3: Gaussian and Laplacian Stacks" style="width: 40%;"/>
</div>

Implement a Gaussian and a Laplacian stack. The different between a stack and a pyramid is that in each level of the pyramid the image is downsampled, so that the result gets smaller and smaller. In a stack the images are never downsampled so the results are all the same dimension as the original image, and can all be saved in one 3D matrix (if the original image was a grayscale image).

Here is the process of creating stacks.

![Part 2.3: Gaussian and Laplacian Stacks](project2_data/2_3/laplacian_apple.png)
![Part 2.3: Gaussian and Laplacian Stacks](project2_data/2_3/laplacian_orange.png)

Apply the Gaussian and Laplacian stacks to the Oraple and recreate the outcomes of Figure 3.42 in Szelski (Ed 2) page 167.

![Part 2.3: Gaussian and Laplacian Stacks](project2_data/2_3/process.png)
![Part 2.3: Gaussian and Laplacian Stacks](project2_data/2_3/final.png)

### Part 2.4: Multiresolution Blending

The masks are produced by [Segment Anything (Research by Meta AI)](https://segment-anything.com)
<div style="display: flex; justify-content: space-around;">
    <div style="text-align: center;">
        <img src="project2_data/2_4/dog.png" alt="dog" style="width: 60%;"/>
        <p>dog</p>
    </div>
    <div style="text-align: center;">
        <img src="project2_data/2_4/plane.jpg" alt="plane" style="width: 60%;"/>
        <p>plane</p>
    </div>
</div>
<div style="display: flex; justify-content: space-around;">
    <div style="text-align: center;">
        <img src="project2_data/2_4/dog2.png" alt="dog2"/>
        <p>dog</p>
    </div>
    <div style="text-align: center;">
        <img src="project2_data/2_4/mask.png" alt="mask"/>
        <p>mask</p>
    </div>
    <div style="text-align: center;">
        <img src="project2_data/2_4/dog_plane.png" alt="dog_plane"/>
        <p>dog_plane</p>
    </div>
</div>


<div style="display: flex; justify-content: space-around;">
    <div style="text-align: center;">
        <img src="project2_data/2_4/cat2.jpg" alt="cat" style="width: 60%;"/>
        <p>cat</p>
    </div>
    <div style="text-align: center;">
        <img src="project2_data/2_4/cake.jpg" alt="cake" style="width: 60%;"/>
        <p>cake</p>
    </div>
</div>
<div style="display: flex; justify-content: space-around;">
    <div style="text-align: center;">
        <img src="project2_data/2_4/cat2_2.png" alt="cat"/>
        <p>cat</p>
    </div>
    <div style="text-align: center;">
        <img src="project2_data/2_4/cat_mask.png" alt="cat_mask"/>
        <p>mask</p>
    </div>
    <div style="text-align: center;">
        <img src="project2_data/2_4/cat_cake.png" alt="cat_cake"/>
        <p>cat_cake</p>
    </div>
</div>