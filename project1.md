<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.7/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

# Project1: Images of the Russian Empire

The goal of this assignment is to take the digitized Prokudin-Gorskii glass plate images and, using image processing techniques, automatically produce a color image with as few visual artifacts as possible.

### Preliminary Idea

All input images are simply divided into slices of 1/3 of the original height, and the L2 norm is used to evaluate the alignment performance. The red and green channels are aligned to the blue channel as the reference. However, the results of this direct alignment are surprisingly poor, as the slices obtained by equally dividing the image have significant black borders around them, which severely affects the alignment accuracy. 

The issues encountered include:  
- The black borders cause significant errors in the alignment algorithm calculations.  
- For high-resolution images, traditional alignment algorithms require a large amount of time.

### Step 1: Crop Before Alignment

To avoid interference from the original black borders on the alignment algorithm, I applied the same proportional cropping to all channels, using only the central portion of each channel for alignment while ignoring the rest. There are various options for this cropping ratio, but to avoid the black borders while retaining enough image information, I chose a 50% cropping rate (in fact, changing this ratio to other similar values will not affect the final alignment result).

After implementing the improvements, my algorithm achieved precise and fast alignment for small images:

<div style="display: flex; justify-content: space-around;">
    <div style="text-align: center;">
        <h6>cathedral.jpg</h6>
        <img src="project1_data\aligned_cathedral.jpg" alt="image1" style="width: 200px;">
        <p>G offset: (5, 2), R offset: (12, 3)</p>
    </div>
    <div style="text-align: center;">
        <h6>monastery.jpg</h6>
        <img src="project1_data\aligned_monastery.jpg" alt="image2" style="width: 200px;">
        <p>G offset: (-3, 2), R offset: (3, 2)</p>
    </div>
    <div style="text-align: center;">
        <h6>tobolsk.jpg</h6>
        <img src="project1_data\aligned_tobolsk.jpg" alt="image3" style="width: 200px;">
        <p>G offset: (3, 3), R offset: (7, 3)</p>
    </div>
</div>
### Step 2: Speed Up Using Image Pyramid

An **image pyramid** is a multi-scale representation of an image, often used in image processing tasks such as alignment, compression, and object detection. The idea is to create a series of progressively smaller images, each formed by downscaling the original image. These smaller images are stacked like a pyramid, with the original image at the base and the smallest image at the top.

<div style="text-align: center;">
    <img src="project1_data/pyramid_example.png" alt="pyramid_example.png" style="zoom:100%;" />
</div>



There are two main types of image pyramids:

1. **Gaussian Pyramid**: Each layer is created by applying a Gaussian blur to the previous layer and then downsampling it. This type of pyramid is often used in coarse-to-fine image alignment or when reducing image detail at different scales.

2. **Laplacian Pyramid**: This pyramid is formed by subtracting each Gaussian layer from the layer above it, effectively capturing the difference in image detail between scales. Laplacian pyramids are useful for image compression and blending, as they represent image details in a more compact form.

Image pyramids allow for faster processing by enabling algorithms to operate on smaller, less detailed versions of the image before refining results on higher-resolution layers. This technique is particularly effective in tasks that require multi-scale analysis or iterative refinement, such as object tracking, image stitching, and alignment.

For small images, the current algorithm has achieved ideal results. However, for high-resolution images, the algorithm takes a considerable amount of time due to exhaustive search. Based on suggestions, I adopted the image pyramid technique to improve the alignment process for high-resolution images. The detailed steps are as follows:

1. **Selecting the upper limit for the highest pyramid layer size**: As the pyramid layers increase, the higher-level images become progressively smaller. However, overly small images lack sufficient information, so I limited the pyramid to a maximum of 4 layers, with the highest layer being 1/8 the size of the original image and the lowest layer being the original size.
2. **Aligning layer by layer from the highest pyramid layer**: Alignment begins at the highest layer, and for each subsequent layer, the displacement found from the previous layer is doubled as the starting point for alignment.
3. **Applying the final displacement obtained from the pyramid**: The final displacement from the pyramid alignment is applied to the cropped channels of the original image to produce the aligned color image.

Using these techniques, the algorithm now only takes 2 to 3 seconds to align a 3k-4k resolution image.

<div style="display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px;">
    <div style="text-align: center;">
        <h6>train.tif</h6>
        <img src="project1_data/aligned_train.jpg" alt="train" style="width: 100%;">
        <p>G offset: (42, 6), R offset: (86, 32)</p>
    </div>
    <div style="text-align: center;">
        <h6>church.tif</h6>
        <img src="project1_data/aligned_church.jpg" alt="monastery" style="width: 100%;">
        <p>G offset: (24, 4), R offset: (58, -4)</p>
    </div>
    <div style="text-align: center;">
        <h6>aligned_harvesters.tif</h6>
        <img src="project1_data/aligned_harvesters.jpg" alt="harvesters" style="width: 100%;">
        <p>G offset: (58, 16), R offset: (124, 14)</p>
    </div>
    <div style="text-align: center;">
        <h6>icon.tif</h6>
        <img src="project1_data/aligned_icon.jpg" alt="icon" style="width: 100%;">
        <p>G offset: (40, 18), R offset: (90, 22)</p>
    </div>
    <div style="text-align: center;">
        <h6>melons.tif</h6>
        <img src="project1_data/aligned_melons.jpg" alt="melons" style="width: 100%;">
        <p>G offset: (82, 10), R offset: (178, 14)</p>
    </div>
    <div style="text-align: center;">
        <h6>onion_church.tif</h6>
        <img src="project1_data/aligned_onion_church.jpg" alt="onion_church" style="width: 100%;">
        <p>G offset: (52, 26), R offset: (108, 36)</p>
    </div>
    <div style="text-align: center;">
        <h6>sculpture.tif</h6>
        <img src="project1_data/aligned_sculpture.jpg" alt="sculpture" style="width: 100%;">
        <p>G offset: (34, -10), R offset: (140, -26)</p>
    </div>
    <div style="text-align: center;">
        <h6>self_portrait.tif</h6>
        <img src="project1_data/aligned_self_portrait.jpg" alt="self_portrait" style="width: 100%;">
        <p>G offset: (78, 28), R offset: (176, 36)</p>
    </div>
    <div style="text-align: center;">
        <h6>three_generations.tif</h6>
        <img src="project1_data/aligned_three_generations.jpg" alt="three_generations" style="width: 100%;">
        <p>G offset: (50, 14), R offset: (110, 12)</p>
    </div>
    <div style="text-align: center;">
        <h6>lady.tif</h6>
        <img src="project1_data/aligned_lady.jpg" alt="sculpture" style="width: 100%;">
        <p>G offset: (50, 8), R offset: (108, 12)</p>
    </div>
    <div style="text-align: center;">
        <h6>emir.tif</h6>
        <img src="project1_data/bad_emir.jpg" alt="self_portrait" style="width: 100%;">
        <p>G offset: (48, 24), R offset: (0, -332)</p>
    </div>
</div>



### Step 3: Addressing the issue of brightness differences in the channels of emir.tif.
Through the optimizations in Step 2, I have established a fast image alignment algorithm, but the alignment effect for emir.tif turned out to be surprisingly poor, with the offset of the red channel clearly miscalculated. 

<div style="text-align: center;">
    <img src="project1_data/bad_emir.jpg" alt="bad_emir" style="zoom: 10%;" />
</div>
Upon reviewing the original channels, I found that the three original channels exhibited completely different brightness levels, and the existing SSD-based alignment method could not handle such images. Therefore, I applied **histogram equalization** to balance the brightness of the three channels, and then used the **structural similarity (SSIM)** method for alignment.



**Brightness Equalization of Three Channels:**


<div style="text-align: center;">
    <img src="project1_data/light.jpg" alt="aligned_emir" style="zoom:12%;" />
</div>

The Structural Similarity Index (SSIM) between two images $$x$$ and $$y$$ is defined as:

$$
SSIM(x, y) = \frac{(2\mu_x \mu_y + c_1)(2\sigma_{xy} + c_2)}{(\mu_x^2 + \mu_y^2 + c_1)(\sigma_x^2 + \sigma_y^2 + c_2)}
$$

where:

- $$\mu_x$$: Mean of $$x$$
- $$\mu_y$$: Mean of $$y$$
- $$\sigma_x^2$$: Variance of $$x$$
- $$\sigma_y^2$$: Variance of $$y$$
- $$\sigma_{xy}$$: Covariance of $$x$$ and $$y$$
- $$c_1$$ and $$c_2$$: Constants to stabilize the division, typically defined as:
  $$
  c_1 = (k_1 L)^2, \quad c_2 = (k_2 L)^2
  $$
- $$L$$: Dynamic range of pixel values (e.g., 255 for 8-bit grayscale images)
- $$k_1$$ and $$k_2$$: Small constants, typically $$ k_1 = 0.01 $$ and $$k_2 = 0.03 $$

**The final result:** g_offset: (48, 22), r_offset: (102, 40)

<div style="text-align: center;">
    <img src="project1_data/aligned_emir.jpg" alt="aligned_emir" style="zoom:12%;" />
</div>

### Step 4: Edge Correction, White Balance, and Image Enhancement (Bells & Whistles)

Here is a Markdown explanation of the white balance adjustment portion of your code, written with LaTeX formulas:

---

### White Balance Adjustment

The `apply_white_balance` function is designed to correct the color balance of an image so that the colors appear more natural and consistent. The process involves adjusting each color channel to match the average intensity of all channels. Here's a step-by-step breakdown:

1. **Convert Image to Float Precision:**
   The image is first converted to a `float32` type to ensure precision during the adjustment process.

   $$
   \text{image} \leftarrow \text{image}.astype(\text{np.float32})
   $$

2. **Calculate Average Intensity for Each Channel:**
   For each color channel (Red, Green, Blue), compute the average pixel value:
    
   $$
   \text{avg}_r = \frac{1}{N} \sum_{i=1}^{N} \text{image}[i, j, 0]
   $$

   $$
   \text{avg}_g = \frac{1}{N} \sum_{i=1}^{N} \text{image}[i, j, 1]
   $$

   $$
   \text{avg}_b = \frac{1}{N} \sum_{i=1}^{N} \text{image}[i, j, 2]
   $$

   where $$N$$ is the total number of pixels in the image, and $$\text{image}[i, j, 0]$$, $$\text{image}[i, j, 1]$$, and $$\text{image}[i, j, 2]$$ represent the pixel values for the Red, Green, and Blue channels, respectively.

3. **Calculate the Overall Average Intensity:**

   $$
   \text{avg}_{\text{gray}} = \frac{\text{avg}_r + \text{avg}_g + \text{avg}_b}{3}
   $$

4. **Adjust Each Channel:**
   Scale each channel so that its average matches the overall average intensity:

   $$
   \text{image}[:, :, 0] \leftarrow \text{image}[:, :, 0] \times \frac{\text{avg}_{\text{gray}}}{\text{avg}_r}
   $$

   $$
   \text{image}[:, :, 1] \leftarrow \text{image}[:, :, 1] \times \frac{\text{avg}_{\text{gray}}}{\text{avg}_g}
   $$

   $$
   \text{image}[:, :, 2] \leftarrow \text{image}[:, :, 2] \times \frac{\text{avg}_{\text{gray}}}{\text{avg}_b}
   $$


5. **Clip Values and Convert Back to `uint8`:**
   Finally, ensure that pixel values are within the valid range $$[0, 255]$$ and convert the image back to `uint8` type:

   $$
   \text{image} \leftarrow \text{np.clip}(\text{image}, 0, 255).astype(\text{np.uint8})
   $$


This method effectively balances the colors by equalizing the average intensity across all color channels, leading to a more visually consistent image.

---

After center cropping to remove borders, performing white balance, and enhancing saturation, the optimized result was obtained.

<div style="display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px;">
    <div style="text-align: center;">
        <h6>train.jpg</h6>
        <img src="project1_data/enhanced/aligned_train.jpg" alt="train" style="width: 100%;">
    </div>
    <div style="text-align: center;">
        <h6>church.jpg</h6>
        <img src="project1_data/enhanced/aligned_church.jpg" alt="church" style="width: 100%;">
    </div>
    <div style="text-align: center;">
        <h6>harvesters.jpg</h6>
        <img src="project1_data/enhanced/aligned_harvesters.jpg" alt="harvesters" style="width: 100%;">
    </div>
    <div style="text-align: center;">
        <h6>icon.jpg</h6>
        <img src="project1_data/enhanced/aligned_icon.jpg" alt="icon" style="width: 100%;">
    </div>
    <div style="text-align: center;">
        <h6>melons.jpg</h6>
        <img src="project1_data/enhanced/aligned_melons.jpg" alt="melons" style="width: 100%;">
    </div>
    <div style="text-align: center;">
        <h6>onion_church.jpg</h6>
        <img src="project1_data/enhanced/aligned_onion_church.jpg" alt="onion_church" style="width: 100%;">
    </div>
    <div style="text-align: center;">
        <h6>sculpture.jpg</h6>
        <img src="project1_data/enhanced/aligned_sculpture.jpg" alt="sculpture" style="width: 100%;">
    </div>
    <div style="text-align: center;">
        <h6>self_portrait.jpg</h6>
        <img src="project1_data/enhanced/aligned_self_portrait.jpg" alt="self_portrait" style="width: 100%;">
    </div>
    <div style="text-align: center;">
        <h6>three_generations.jpg</h6>
        <img src="project1_data/enhanced/aligned_three_generations.jpg" alt="three_generations" style="width: 100%;">
    </div>
    <div style="text-align: center;">
        <h6>lady.jpg</h6>
        <img src="project1_data/enhanced/aligned_lady.jpg" alt="sculpture" style="width: 100%;">
    </div>
    <div style="text-align: center;">
        <h6>emir.jpg</h6>
        <img src="project1_data/enhanced/aligned_emir.jpg" alt="self_portrait" style="width: 100%;">
    </div>
    <div style="text-align: center;">
        <h6>monastery.jpg</h6>
        <img src="project1_data/enhanced/monastery_out.jpg" alt="monastery" style="width: 100%;">
    </div>
</div>