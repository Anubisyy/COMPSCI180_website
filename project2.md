# CS 180 Project 2: Fun with Filters and Frequencies

## Part 1: Fun with Filters
In this part, we will build intuitions about 2D convolutions and filtering.

### Part 1.1: Finite Difference Operator

We will begin by using the humble finite difference as our filter in the x and y directions.

\[D_x = \begin{bmatrix}
1 & -1
\end{bmatrix},\ \ 
D_y = \begin{bmatrix}
1 \\
-1
\end{bmatrix}\]

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
![Part 2.1: Image "Sharpening"](project2_data/taj.png)
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
![Part 2.2: Hybrid Images](project2_data/2_2/1.png)

![Part 2.2: Hybrid Images](project2_data/2_2/2.png)
![Part 2.2: Hybrid Images](project2_data/2_2/3.png)
![Part 2.2: Hybrid Images](project2_data/2_2/4.png)


## Multi-resolution Blending and the Oraple journey

### Part 2.3: Gaussian and Laplacian Stacks

<div style="text-align: center;">
    <img src="project2_data/2_3/mask.png" alt="Part 2.3: Gaussian and Laplacian Stacks" style="width: 40%;"/>
</div>
Process:

![Part 2.3: Gaussian and Laplacian Stacks](project2_data/2_3/laplacian_apple.png)
![Part 2.3: Gaussian and Laplacian Stacks](project2_data/2_3/laplacian_orange.png)
Result:
![Part 2.3: Gaussian and Laplacian Stacks](project2_data/2_3/process.png)
![Part 2.3: Gaussian and Laplacian Stacks](project2_data/2_3/final.png)

### Part 2.4: Multiresolution Blending
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
</div>

![Part 2.3: Gaussian and Laplacian Stacks](project2_data/2_4/dog_plane.png)


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
</div>

![Part 2.3: Gaussian and Laplacian Stacks](project2_data/2_4/cat_cake.png)