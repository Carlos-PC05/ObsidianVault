#DeepLearning 
## Diapositivas

[chapter1.pdf](attachment:4ce28e9b-3f3f-4e43-80e9-5b3aeb815b7a:chapter1.pdf)

[chapter2.pdf](attachment:946db2a6-2986-44bb-8b94-ae8465fde485:chapter2.pdf)

[chapter3.pdf](attachment:8a6a700e-5c74-4652-8038-2e659dcf8782:chapter3.pdf)

[chapter4.pdf](attachment:a2d8b4ba-2d27-41d1-b61e-2636a8e63f26:chapter4.pdf)

# Exploration

## Load images

```python
# Import ImageIO
import imageio

# Load "chest-220.dcm"
im = imageio.imread("chest-220.dcm") 

# Print image attributes
print('Image type:', type(im))
print('Shape of image array:', im.shape)
```

usamos el método `imread()` para leer una imagen

## Metadata

```python
# Import ImageIO
import imageio
im = imageio.imread('chest-220.dcm')

# Print the available metadata fields
print(im.meta.keys())
```

## Plot images

```python
# Import ImageIO and PyPlot -> librerías necesarias
import imageio
import matplotlib.pyplot as plt

# Read in "chest-220.dcm"
im = imageio.imread("chest-220.dcm") #leemos imagen
plt.imshow(im, cmap = "gray") #la ploteamos
plt.show()
```

Podemos aumentar el contraste de la fotografía con los parámetros vmin y vmax:

`plt.imshow(im, cmap = "gray", vmin = -200, vmax = 200)`

`plt.axis('off')` removes axis and tick labels from the image.

## Loading volumes

read multi-dimensional data directly

assemble a volume from multiple images

```python
import os
import imageio

#multidimensional data
os.listdir("chest-data")

vol = imageio.volread("chest_data")

#image shape -> number of elements along each axis
vol.shape
n0, n1, n2 = vol.shape

#Sampling rate -> physical space covered by each element
d0, d1, d2 = vol.meta['sampling']

#Field of view -> physical space covered along each axis
n0 * d0, n1 * d1, n2 * d2
```

## Stack images

Image "stacks" are a useful metaphor for understanding multi-dimensional  
data. Each higher dimension is a stack of lower dimensional arrays.

![](https://assets.datacamp.com/production/repositories/2085/datasets/b540e2aca266bd28b0465a97f5c424efb285cd8d/Ch1_L2_Stacks4D.PNG)

In this exercise, we will use NumPy's `stack()` function to combine several 2D arrays into a 3D volume. [By convention](http://scikit-image.org/docs/dev/user_guide/numpy_images.html#numpy-images-coordinate-conventions), volumetric data should be stacked along the first dimension: `vol[plane, row, col]`.

_Note: performing any operations on an ImageIO `Image` object will convert it to a `numpy.ndarray`, stripping its metadata._

## Advanced plotting

`plt.subplots(nrows, ncols)` → creates a figure canvas with multiple AxesSubplots objects

![Captura de pantalla 2026-02-19 145041.png](attachment:6a3fbde1-51e7-4b3c-b8c9-b348c3b071ec:Captura_de_pantalla_2026-02-19_145041.png)

cada posición de la matriz axes[] es una imagen. Por ejemplo si queremos dibujar las imágenes [0,0] y [0,1] lo haríamos de la siguiente forma:

```python
# Draw an image on each subplot
axes[0].imshow(im1, cmap='gray')
axes[0].axis('off')   #Con esta línea le quitamos los ejes o bordes a la gráfica
plt.imshow

# Remove ticks/labels and render
axes[1].imshow(im2, cmap='gray')
axes[1].axis('off')
plt.show()
```

To select a 2D frame, pick a frame for the first axis and select all data from the remaining two: `vol[0, :, :]` → así elegimos una ‘slice 2D’ de la imagen 3D que queremos plotear.

# Masks and filters

## Histograms

**Histograms** display the distribution of values in your image by binning (clasificar) each element by its intensity then measuring the size of each bin.

The area under a histogram is called the **cumulative distribution function**. It measures the frequency with which a given range of pixel intensities occurs.

```python
#import
import scipy.dimage as ndi

#Crear histograma
hist = ndi.histogram(
	im,
	min = 0,
	max = 255,
	bins = 256
)
```

### Equalization

Redistribute values to optimize full intensity range

Steps:

1. Generar el histograma
2. Calcular la función de distribución acumulativa

```python
cdf = hist.cumsum() / hist.sum()
csf.shape
```

1. Aplicamos la función a la imagen y reescalamos a 255

```python
im.equalized = cf[im] * 255
```

1. Trazamos imagen

```python
fig, axes = subplots(2, 1)
axes[0].imshow(im)
axes[1].imshow(im.equialized)
plt.show
```

## Masks

**Masks** are the primary method for removing or selecting specific parts of an image. They are binary arrays that indicate whether a value should be included in an analysis. Typically, masks are created by applying one or more logical operations to an image.

![Captura de pantalla 2026-02-22 025600.png](attachment:db87355e-b61e-4478-8e89-dcc8cb1cc559:Captura_de_pantalla_2026-02-22_025600.png)

Although masks are binary, they can be _applied_ to images to filter out pixels where the mask is `False`.

NumPy's `where()` function is a flexible way of applying masks. It takes three arguments:

```
np.where(condition, x, y)
```

`condition`, `x` and `y` can be either arrays or single values. This allows you to pass through original image values while setting masked values to 0.

![Captura de pantalla 2026-02-22 025924.png](attachment:1e106db6-5748-4970-a67e-3820adb82ca6:Captura_de_pantalla_2026-02-22_025924.png)

### Tune a mask

Imperfect masks can be tuned through the addition and subtraction of  
pixels. SciPy includes several useful methods for accomplishing these  
ends. These include:

- `binary_dilation`: Add pixels along edges
- `binary_erosion`: Remove pixels along edges
- `binary_opening`: Erode then dilate, "opening" areas near edges
- `binary_closing`: Dilate then erode, "filling in" holes

## Filters

_Filters_ are an essential tool in image processing. They allow you to transform images based on intensity values surrounding a pixel, rather than globally.

### Filter functions

Convolutions rely on a set of weights, but filtering can also be done using functions such as the mean, median and maximum. Just like with convolutions, filter functions will update each pixel value based on its local neighborhood.

![Captura de pantalla 2026-02-22 033013.png](attachment:87d47bc1-233f-46bf-a28c-4516d1fc8e8b:Captura_de_pantalla_2026-02-22_033013.png)

Smoothing can improve the [**signal-to-noise ratio](https://en.wikipedia.org/wiki/Signal-to-noise_ratio)** of your image by blurring out small variations in intensity. The Gaussian filter is excellent for this: it is a circular (or spherical) smoothing kernel that weights nearby pixels higher than distant ones.

![](https://assets.datacamp.com/production/repositories/2085/datasets/f5c784ab505d261728f7a3eb40225e87d4526c9f/Ch2_L2_Gaussian1D.png)

The width of the distribution is controlled by the `sigma` argument, with higher values leading to larger smoothing effects.

## Feature detection

Filters can also be used as "detectors." If a part of the image fits the weighting pattern, the returned value will be very high (or very low).

In the case of edge detection, that pattern is a change in intensity along a plane. A filter detecting horizontal edges might look like this:

```
weights = [[+1, +1, +1],
           [ 0,  0,  0],
           [-1, -1, -1]]
```

Edge detection can be performed along multiple axes, then combined into a single edge value. For 2D images, the horizontal and vertical "edge maps" can be combined using the Pythagorean theorem

One popular edge detector is the [Sobel filter](https://en.wikipedia.org/wiki/Sobel_operator). The Sobel filter provides extra weight to the center pixels of the detector:

```
weights = [[ 1,  2,  1],
           [ 0,  0,  0],
           [-1, -2, -1]]
```

# Measurement

## Objects and labels

Labels are like object "handles" - they give you a way to pick up whole sets of pixels at a time. To select a particular object:

1. Find the label value associated with the object.
2. Create a mask of matching pixels.

```python
labels, nlabels = ndi.label(mask) #return an array where all objects have been indexed

#Select pixels from the first object
np.where(labels == 1, im, 0) #where label is 1, return the value form "im" else return 0
```

Extracting objects from the original image eliminates unrelated pixels and provides new images that can be analyzed independently.

The key is to crop images so that they only include the object of interest. The range of pixel indices that encompass the object is the [_bounding box_](https://en.wikipedia.org/wiki/Minimum_bounding_box).

```python
ndi.find_objects(labels) #return array of tuples
```

## Measuring intensity

![Captura de pantalla 2026-02-23 193351.png](attachment:24850164-4f5b-4109-a1a6-27b36cf4e30e:Captura_de_pantalla_2026-02-23_193351.png)

SciPy measurement functions allow you to tailor measurements to specific sets of pixels:

- Specifying `labels` restricts the mask to non-zero pixels.
- Specifying `index` value(s) returns a measure for each label value.

```python
#-----Ejemplo de código de un ejercicio------

# Create histograms for selected pixels
hist1 = ndi.histogram(vol, min=0, max=255, bins=256)
hist2 = ndi.histogram(vol, 0, 255, 256, labels=labels)
hist3 = ndi.histogram(vol, 0, 255, 256, labels=labels, index=1)

# Plot the histogram density
plt.plot(hist1 / hist1.sum(), label='All pixels')
plt.plot(hist2 / hist2.sum(), label='All labeled pixels')
plt.plot(hist3 / hist3.sum(), label='Left ventricle')
format_and_render_plot()
```

## Measuring Morphology

### Calculate volume

Quantifying tissue _morphology_, or shape is one primary objective of biomedical imaging. The size,  
shape, and uniformity of a tissue can reveal essential health insights.

```python
#Calculate volume per voxel
d0, d1, d2 = vol.meta['sampling']
dvoxel = d0 * d1 * d2

#Count label voxels
nvoxels = dni.sum(1, label, index = 1)

#Calculate volume of label
volume = nvoxels * dvoxel
```

### Pinpoint center of mass

The distance transformation reveals the most embedded portions of an object. On the other hand, `ndi.center_of_mass()` returns the coordinates for the center of an object.

The "mass" corresponds to intensity values, with higher values pulling the center closer to it.

## Measuring on time

The [**ejection fraction**](https://en.wikipedia.org/wiki/Ejection_fraction) is the proportion of blood squeezed out of the left ventricle each heartbeat. To calculate it, radiologists have to identify the maximum volume (systolic volume) and the minimum volume (diastolic volume) of the ventricle.

The ejection fraction is defined as:

$$  
\frac{V_{max} - V_{min}}{V_{max}}  
$$

where is left ventricle volume for one 3D timepoint.

To close our investigation, plot slices from the maximum and minimum volumes by analyzing the volume time series (`ts`). Then, calculate the ejection fraction.

# Image Comparison

## Spatial Transformations

In this chapter, we'll leverage data use data from the [Open Access Series of Imaging Studies](https://www.oasis-brains.org/) to compare the brains of different populations: young and old, male and female, healthy and diseased.

To start, center a single slice of a 3D brain volume (`im`). First, find the center point in the image array and the center of mass of the brain. Then, translate the image to the center.

```python
# Find image center of mass
com = ndi.center_of_mass(im)

# Calculate amount of shift needed
d0 = 128 - com[0]
d1 = 128 - com[1]

# Translate the brain towards the center
xfm = ndi.shift(im, shift=(d0, d1))

# Plot the original and adjusted images
fig, axes = plt.subplots(2, 1)
axes[0].imshow(im)
axes[1].imshow(xfm)
format_and_render_plot()
```

In cases where an object is angled or flipped, the image can be rotated. Using `ndi.rotate()`, the image is rotated from its center by the specified degrees from the right horizontal axis.

```python
# Shift the image towards the center
xfm = ndi.shift(im, shift=[-20, -20])

# Rotate the shifted image
xfm = ndi.rotate(xfm, angle=-30, reshape=False)

# Plot the original and rotated images
fig, axes = plt.subplots(2, 1)
axes[0].imshow(im)
axes[1].imshow(xfm)
format_and_render_plot()
```

### Affine transform

An affine transformation matrix provides directions for up to four types of changes: translating, rotating, rescaling and shearing. The elements of the matrix map the coordinates from the input array to the output.

## Resampling and interpolation

### Resampling

Images can be collected in a variety of shapes and sizes. _Resampling_ is a useful tool when these shapes need to be made consistent. Two common applications are:

- **Downsampling**: combining pixel data to _decrease_ size
- **Upsampling**: distributing pixel data to _increase_ size

```python
# Center and level image
xfm = ndi.shift(im, shift=(-20, -20))
xfm = ndi.rotate(xfm, angle=-35, reshape=False)

# Resample image
im_dn = ndi.zoom(xfm, zoom=0.25)
im_up = ndi.zoom(xfm, zoom=4)

# Plot the images
fig, axes = plt.subplots(2, 1)
axes[0].imshow(im_dn)
axes[1].imshow(im_up)
format_and_render_plot()
```

### Interpolation

_Interpolation_ is how new pixel intensities are estimated when an image transformation is applied. It is implemented in SciPy using [sets of spline functions](https://docs.scipy.org/doc/scipy/reference/tutorial/interpolate.html).

Editing the interpolation `order` when using a function such as `ndi.zoom()` modifies the resulting estimate: higher orders provide more flexible estimates but take longer to compute.

```python
# Upsample "im" by a factor of 4
up0 = ndi.zoom(im, zoom=4, order=0)
up5 = ndi.zoom(im, zoom=4, order=5)

# Print original and new shape
print('Original shape:', im.shape)
print('Upsampled shape:', up0.shape)

# Plot close-ups of the new images
fig, axes = plt.subplots(1, 2)
axes[0].imshow(up0[128:256, 128:256])
axes[1].imshow(up5[128:256, 128:256])
format_and_render_plots()
```

## Comparing images

### Mean absolute error

_Cost functions_ and _objective functions_ output a single value that summarizes how well two images match.

The **mean absolute error** (MAE), for example, summarizes intensity differences between two images, with higher values indicating greater divergence.

```python
err = im1 - im2
pl.imshow(err)

#abs err
abs_err = np.abs(err)

#MAE
mae = np.mean(abs_err)
```

### Intersection of the union

Another cost function is the intersection of the union (IOU). The IOU is the number of pixels filled in both images (the _intersection_) out of the number of pixels filled in either image (the _union_).

For this exercise, determine how best to transform `im1` to **maximize** the IOU cost function with `im2`. We have defined the following function for you:

## Normalizing Measurements

### Identifying potential confounds

Once measures have been extracted, double-check for dependencies within your data. This is especially true if any image parameters (sampling rate, field of view) might differ between subjects, or you pull multiple measures from a single image.