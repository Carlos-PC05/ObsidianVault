#DeepLearning 
# Slides

[chapter1](attachment:7847cc73-f11a-4248-9cb5-39b4e8eaa24f:chapter1-6_compressed.pdf)

[chapter2](attachment:45ee1938-7bf6-4d7f-8fe3-8f5e4ebc5fb3:chapter2-6_compressed.pdf)

[chapter3](attachment:4e9acc2d-b8dd-44b5-89d9-7c69dee26898:chapter3-2_compressed.pdf)

[chapter_4.pdf](attachment:2dda67e9-f65d-44fa-b551-db864dc4b559:chapter4_compressed.pdf)

# Notes

## Image Classification with CNNs

### Binary and multi-class image classification

#### The number of classes

To determine whether to use a binary or a multi-class model for a classification problem, you need to know the number of classes in the data. The Torch dataset is available in your workspace as `train_dataset`.

Considering the number of classes in `train_dataset`, which image classification task would be appropriate?

`len(train_dataset.classes)` → 2

#### Binary classification model

As a deep learning practitioner, one of your main tasks is training models for image classification. You often encounter binary classification, where you need to distinguish between two classes. To  
streamline your workflow and ensure reusability, you have decided to create a template for a binary image classification CNN model, which can be applied to future projects.

The package `torch` and `torch.nn` as `nn` have been imported. All image sizes are 64x64 pixels.

```python
class BinaryImageClassifier(nn.Module):
    def __init__(self):
        super(BinaryImageClassifier, self).__init__()
        
        # Create a convolutional layer
        self.conv1 = nn.Conv2d(
            in_channels = 3,
            out_channels = 16, 
            kernel_size = 3,
            stride = 1,
            padding = 1
        )
        self.relu = nn.ReLU()
        self.maxpool = nn.MaxPool2d(kernel_size=2, stride=2)
        self.flatten = nn.Flatten()
        
        # Create a fully connected layer
        self.fc = nn.Linear(
            16*32*32,
            1
        )
        
        # Create an activation function
        self.sigmoid = nn.Sigmoid()
```

#### Multi-class classification model

With a template for a binary classification model in place, you can now build on it to design a multi-class classification model. The model should handle different numbers of classes via a parameter, allowing you to tailor the model to a specific multi-class classification task in the future.

The packages `torch` and `torch.nn` as `nn` have been imported. All image sizes are 64x64 pixels.

```python
class MultiClassImageClassifier(nn.Module):
  
    # Define the init method
    def __init__(self, num_classes):
        super(MultiClassImageClassifier, self).__init__()
        self.conv1 = nn.Conv2d(3, 16, kernel_size=3, stride=1, padding=1)
        self.relu = nn.ReLU()
        self.maxpool = nn.MaxPool2d(kernel_size=2, stride=2)
        self.flatten = nn.Flatten()

        # Create a fully connected layer
        self.fc = nn.Linear(16*32*32, num_classes)
        
        # Create an activation function
        self.softmax = nn.Softmax(1)
```

### Convolutional layers for images

#### RGB, grayscale, or alpha?

Your team obtained a new image dataset for training. Before designing a model, you want to check if the images are RGB, grayscale, or with a transparency alpha channel. Assuming all the images in the dataset are the same, you only need to check one sample image.

The `torchvision.transforms.functional` module has been imported as `F`. The sample image to check has been loaded as `image`.

What kind of images does the dataset consist of?

`F.get_image_num_channels(image)` → 3 (RGB)

#### Adding a new convolutional layer

Your project lead provided you with a new CNN model. Let's take a look at the model's architecture and append a new convolutional layer to it.

The model is available as `CNNModel`. The packages `torch` and `torch.nn` as `nn` have been imported.

```python
# Create a model
model = CNNModel()
print("Original model: ", model)

# Create a new convolutional layer
conv2 = nn.Conv2d(
    in_channels = 1,
    out_channels = 32,
    stride = 1,
    padding = 1,
    kernel_size = 3
)

# Append the new layer to the model
model.add_module('conv2', conv2)
print("Extended model: ", model)
```

#### Creating a sequential block

You decided to redesign your binary CNN model template by creating a block of convolutional layers. This will help you stack multiple layers sequentially. With this improved model, you will be able to easily design various CNN architectures.

```python
class BinaryImageClassification(nn.Module):
  def __init__(self):
    super(BinaryImageClassification, self).__init__()
    # Create a convolutional block
    self.conv_block = nn.Sequential(
      nn.Conv2d(3, 16, kernel_size=3, stride=1, padding=1),
      nn.ReLU(),
      nn.Conv2d(16, 32, kernel_size=3, stride=1, padding=1),
      nn.ReLU(),
    )
    
  def forward(self, x):
    # Pass inputs through the convolutional block
    x = self.conv_block(x)
    return x
```

### Working with pre-trainded models

#### Save and load a model

A manufacturing company wants to classify their projects based on images and determine the appropriate shipping packaging. Having trained a highly accurate model in PyTorch, you now plan to save the model and its pre-trained weights for future use and to share it with your team, making sure they can seamlessly load it.

`torch` and `torch.nn` as `nn` have been imported. The pre-trained model object is available in your workspace as `model`, and its architecture as `ManufacturingCNN`.

```python
# Save the model
torch.save(model.state_dict(), 'ModelCNN.pth')

# Create a new model
loaded_model = ManufacturingCNN()

# Load the saved model
loaded_model.load_state_dict(torch.load('ModelCNN.pth'))
print(loaded_model)
```

#### Loading a pre-trained model

You are building an application to label images from the social media. This task requires high accuracy and speed. You are going to use a pre-trained ResNet18 model to infer image classes.

```python
# Import resnet18 model
from torchvision.models import resnet18, ResNet18_Weights

# Initialize model with default weights
weights = ResNet18_Weights.DEFAULT
model = resnet18(weights = weights)

# Set model to evaluation mode
model.eval()

# Initialize the transforms
transform = weights.transforms()
```

#### Image classification with ResNet

You have created the model from the pre-trained ResNet18. Now, it is time to test it on an example image.

You are going to apply preprocessing transforms to an image and classify it. You will need to use the `softmax()` layer followed by the `argmax()`, since ResNet18 has been trained on a multi-class dataset.

You have selected the following image to use for prediction testing:

![A cup of espresso](https://assets.datacamp.com/production/repositories/6315/datasets/91fd969fe0905e82af6ba5419d53777eb2395882/Espresso.jpeg)

The preprocessing transform is saved as `preprocess`. The PIL image is uploaded as `img`.

```python
# Apply preprocessing transforms
batch = preprocess(img).unsqueeze(0)

# Apply model with softmax layer
prediction = model(batch).squeeze(0).softmax(0)

# Apply argmax
class_id = prediction.argmax().item()
score = prediction[class_id].item()
category_name = weights.meta["categories"][class_id]
print(category_name)
```

## Object Recognition

### Bounding boxes

#### Image tensors

A coffee company has an object detection project where they need to annotate objects of interest, in this case, espresso shots. You have created a list with the bounding box coordinates for an espresso shot image. Now, you need to convert the image and the coordinates into tensors.

`torch` and `torchvision` have been imported. `torchvision.transforms` is imported as `transforms`. The image has been loaded as `image` using `Image.open()` from `PIL` library. The bounding box coordinates are stored in the variable `bbox`.

```python
# Convert bbox into tensors
bbox_tensor = torch.tensor(bbox)

# Add a new batch dimension
bbox_tensor = bbox_tensor.unsqueeze(0)

# Resize image and transform tensor
transform = transforms.Compose([
  transforms.Resize(224),
  transforms.PILToTensor()
])

# Apply transform to image
image_tensor = transform(image)
print(image_tensor)
```

#### Drawing a bounding box

Having prepared the image and bounding box tensors, you can now draw the box on top of the image and visually inspect its accuracy.

`torch`, `torchvision`,`torchvision.transforms` have been imported. Image has been already transformed to tensors as `image_tensor`. The coordinates have been assigned to the variables: `x_min`, `y_min`, `x_max`, `y_max`.

```python
# Import draw_bounding_boxes
from torchvision.utils import draw_bounding_boxes

# Define the bounding box coordinates
bbox = [x_min, y_min, x_max, y_max]
bbox_tensor = torch.tensor(bbox).unsqueeze(0)

# Implement draw_bounding_boxes
img_bbox = draw_bounding_boxes(image_tensor, bbox_tensor, width=3, colors="red")

# Tranform tensors to image
transform = transforms.Compose([
    transforms.ToPILImage()
])
plt.imshow(transform(img_bbox))
plt.show()
```

![[Pasted image 20260806095130.png]]
### Evaluating object recognition models

#### Bounding boxes prediction

You have trained an object recognition model. Now, you want to use to generate bounding boxes and classifications for a test image.

The model is available as `model` and it's already in the evaluation mode. The `test_image` is also available, and `torch` and `torch.nn` as `nn` have been imported.

```python
# Get model's prediction
with torch.no_grad():
    output = model(test_image)

# Extract boxes from the output
boxes = output[0]['boxes']

# Extract scores from the output
scores = output[0]['scores']

print(boxes, scores)
```

#### Calculate NMS

Having extracted the predicted bounding boxes and scores from your object recognition model, your next task is to ensure that only the most accurate and non-overlapping predicted bounding boxes are retained by using the non-max suppression technique.

`boxes` and `scores` you created in the previous exercise are available in your workspace and `torch` and `torchvision` have been imported.

```python
# Import nms
from torchvision.ops import nms

# Set the IoU threshold
iou_threshold = 0.5

# Apply non-max suppression
box_indices = nms(boxes, scores, iou_threshold)

# Filter boxes
filtered_boxes = boxes[box_indices]

print("Filtered Boxes:", filtered_boxes)
```

### Object detection usin R-CNN

#### Pre-trained model backbone

It's time to build an R-CNN architecture! You will use the `vgg16` pre-trained model's backbone for feature extraction. You also remember to store the output shape of the backbone which will serve as the input shape for the subsequent blocks: the classifier and the box regressor.

`torch`, `torchvision`, `torch.nn` as `nn` have been imported. The model has been imported as `vgg16` with the weights stored in `VGG16_Weights`.

```python
# Load pretrained weights
vgg_model = vgg16(weights=VGG16_Weights.DEFAULT)

# Extract the input dimension
input_dim = nn.Sequential(*list(vgg_model.classifier.children()))[0].in_features

# Create a backbone with convolutional layers
backbone = nn.Sequential(*list(vgg_model.features.children()))

# Print the backbone model
print(backbone)
```

#### Classifier block

Your next task is to create a classifier block that will replace the original VGG16 classifier. You decide to use a block with two fully connected layers with a ReLU activation in between.

```python
# Create a variable with the number of classes
num_classes = 2
    
# Create a sequential block
classifier = nn.Sequential(
	# Create a linear layer with input features
	nn.Linear(num_classes, 512),
	nn.ReLU(),
	# Add the output dimension to the classifier
	nn.Linear(512, 1),
)
```

#### Box regressor block

Your final task is to create a regressor block to predict bounding box coordinates. You decide to have a block with 2 fully connected layers with a ReLU activation in between, similar to the classifier you defined earlier.

```python
# Define the number of coordinates
num_coordinates = 4

bb = nn.Sequential(  
	# Add input and output dimensions
	nn.Linear(num_coordinates, 32),
	nn.ReLU(),
	# Add the output for the last regression layer
	nn.Linear(32, 1),
)
```

### Regression network proposals with Faster R-CNN

#### Anchor generator

Your team is developing object detection models based on the Faster R-CNN architecture and using pre-trained backbones. Your task is to create anchor boxes to serve as reference bounding boxes for proposing potential object regions.

You will create 9 standard anchors (3 box sizes and 3 aspect ratios).

```python
# Import AnchorGenerator
from torchvision.models.detection.rpn import AnchorGenerator

# Configure anchor size
anchor_sizes = ((32, 64, 128),)

# Configure aspect ratio
aspect_ratios = ((0.5, 1.0, 2.0),)

# Instantiate AnchorGenerator
rpn_anchor_generator = AnchorGenerator(anchor_sizes, aspect_ratios)
```

#### Faster R-CNN model

Your next task is to build a Faster R-CNN model that can detect objects of different sizes in an image. For this task, you will be using a handy class `MultiScaleRoIAlign()` from `torchvision.ops`.

`FasterRCNN` class has been imported from `torchvision.models.detection`. Your `anchor_generator` from the last exercise is available in your workspace and `torch`, `torch.nn` as `nn`, and `torchvision` have been imported.

```python
# Import MultiScaleRoIAlign
from torchvision.ops import MultiScaleRoIAlign

# Instantiate RoI pooler
roi_pooler = MultiScaleRoIAlign(
	featmap_names = ["0"],
	output_size = 7,
	sampling_ratio = 2,
)

mobilenet = torchvision.models.mobilenet_v2(weights="DEFAULT")
backbone = nn.Sequential(*list(mobilenet.features.children()))
backbone.out_channels = 1280

# Create Faster R-CNN model
model = FasterRCNN(
	backbone=backbone,
	num_classes=2,
	anchor_generator=anchor_generator,
	box_roi_pool=roi_pooler,
)
```

#### Define losses for RPN and R-CNN

You are planning to train an object detection model that utilizes both the RPN and R-CNN components. To be able to train it, you will need to define the loss function for each component.

You remember that the RPN component classifies whether a region contains an object and predicts the bounding box coordinates for the proposed regions.The R-CNN component classifies the object into one of multiple classes while also predicting the final bounding box coordinates.

```python
# Implement the RPN classification loss function
rpn_cls_criterion = nn.BCEWithLogitsLoss()

# Implement the RPN regression loss function
rpn_reg_criterion = nn.MSELoss

# Implement the R-CNN classification Loss function
rcnn_cls_criterion = nn.CrossEntropyLoss()

# Implement the R-CNN regression loss function
rcnn_reg_criterion = nn.MSELoss()
```

## Image Segmentation

### Introduction to image segmentation

#### Creating binary masks

Images for segmentation tasks are typically annotated with pixel-level masks. Consider this image of an Egyptian Mau cat.

![cat image](https://assets.datacamp.com/production/repositories/6315/datasets/64f48224174ca9c49d7db7079fd43e6a8749723c/Egyptian_Mau_123.jpg)

In this and the next exercise, you will use the corresponding mask to segment the cat out of the image. First, you will need to load the mask and binarize it.

```python
# Load mask image
mask = Image.open('annotations/Egyptian_Mau_123.png')

# Transform mask to tensor
transform = transforms.Compose([transforms.ToTensor()])
mask_tensor = transform(mask)

# Create binary mask
binary_mask = torch.where(
    mask_tensor == 1/255, 
    torch.tensor(1.0),
    torch.tensor(0.0),
)

# Print unique mask values
print(binary_mask.unique())
```

#### Segmenting image with a mask

With the binary mask ready, you can use it to segment the object, that is the cat, out of the image.

To do so, you will need to load the original image and transform it to a tensor. Next, you will create the object tensor by masking the original image. Finally, you will display the result.

```python
# Load image and transform to tensor
image = Image.open("images/Egyptian_Mau_123.jpg")
transform = transforms.Compose([transforms.ToTensor()])
image_tensor = transform(image)

# Segment object out of the image
object_tensor = image_tensor * binary_mask

# Convert segmented object to image and display
to_pil_image = transforms.ToPILImage()
object_image = to_pil_image(object_tensor)
plt.imshow(object_image)
plt.show()
```

### Instance segmentation with mask R-CNN

#### Segmenting with pre-trained Mask R-CNN

In this exercise, you will use the pre-trained Mask R-CNN model to perform instance segmentation on the following image of two cats.

![two cats image](https://assets.datacamp.com/production/repositories/6315/datasets/5c9199ebd3ca5e3f1e29ea64545231042c3d53f2/two_cats.jpg)

The model you will use has been pre-trained on the [COCO dataset](https://cocodataset.org/#home), which contains images of common objects, including animals. Thanks to this, the model should be able to recognize cats out of the box, without the need to fine-tune it.

Your task is to load the model and the two cats image, prepare the image, and pass it to the model to obtain the predictions. `Image` from `PIL`, `torch`, `transforms` from `torchvision`, and `maskrcnn_resnet50_fpn` have been imported for you.

```python
# Load a pre-trained Mask R-CNN model
model = maskrcnn_resnet50_fpn(pretrained = True)
model.eval()

# Load an image and convert to a tensor
image = Image.open("two_cats.jpg")
transform = transforms.Compose([transforms.ToTensor()])
image_tensor = transform(image).unsqueeze(0)

# Perform inference
with torch.no_grad():
    prediction = model(image_tensor)
    print(prediction)
```

#### Displaying soft masks

In the previous exercise, you have learned that the top two most likely objects the Mask R-CNN model has segmented are both cats. Now, you will display the masks for these two cats overlaid on top of the original image to visually verify their accuracy. This will require iterating over the two masks, and for each of them, plotting the original image followed by a semi-transparent mask on top of it.

```python
# Extract masks and labels from prediction
masks = prediction[0]['masks']
labels = prediction[0]['labels']

# Plot image with two overlaid masks
for i in range(2):
    plt.imshow(image)
    # Overlay the i-th mask on top of the image
    plt.imshow(masks[i,0], cmap = 'jet', alpha = 0.5)
    plt.title(f"Object: {class_names[labels[i]]}")
    plt.show()
```

### Semantic Segmentation with U-Net

#### Building a U-Net: layers definitions

In this and the next exercise, you will construct a U-Net architecture from scratch. Here, you start with defining the `__init__()` method where you will define layers and blocks of layers for the model to use.

The encoder and transposed convolution have already been defined for you. What's left is the decoder's convolutional blocks. You need to pass the appropriate number of input and output channels to each of them, taking into account the skip connections.

The first block, `dec1`, will take as input the concatenation of `upconv3` output with the `enc3` output. The `dec1` output, in turn, should be equal to `enc3` output. Can you fill-in all the missing input and output sizes?

```python
class UNet(nn.Module):
    def __init__(self, in_channels, out_channels):
        super(UNet, self).__init__()

        self.enc1 = self.conv_block(in_channels, 64)
        self.enc2 = self.conv_block(64, 128)
        self.enc3 = self.conv_block(128, 256)
        self.enc4 = self.conv_block(256, 512)

        self.pool = nn.MaxPool2d(kernel_size=2, stride=2)

        self.upconv3 = nn.ConvTranspose2d(512, 256, kernel_size=2, stride=2)
        self.upconv2 = nn.ConvTranspose2d(256, 128, kernel_size=2, stride=2)
        self.upconv1 = nn.ConvTranspose2d(128, 64, kernel_size=2, stride=2)
        
        # Define the decoder blocks
        self.dec1 = self.conv_block(512, 256)
        self.dec2 = self.conv_block(256, 128)
        self.dec3 = self.conv_block(128, 64)

        self.out = nn.Conv2d(64, out_channels, kernel_size=1)
```

#### Building a U-Net: forward method

With the encoder and decoder layers defied, you can now implement the `forward()` method of the U-net. The inputs have already been passed through the encoder for you. However, you need to define the last decoder block.

The goal of the decoder is to upsample the feature maps so that its output is of the same height and width as the U-Net's input image. This will allow you to obtain pixel-level semantic masks.

```python
def forward(self, x):
    x1 = self.enc1(x)
    x2 = self.enc2(self.pool(x1))
    x3 = self.enc3(self.pool(x2))
    x4 = self.enc4(self.pool(x3))

    x = self.upconv3(x4)
    x = torch.cat([x, x3], dim=1)
    x = self.dec1(x)

    x = self.upconv2(x)
    x = torch.cat([x, x2], dim=1)
    x = self.dec2(x)

    # Define the last decoder block with skip connections
    x = self.upconv1(x)
    x = torch.cat([x, x1], dim=1)
    x = self.dec3(x)

    return self.out(x)
```

#### Running semantic segmentation

Good job designing the U-Net! You will find an already pre-trained model very similar to the one you have just built available to you. This model has been trained on a large set of images and contains a few small additions to the architecture, such as the batch norm layers.

You can instantiate the model as `UNet()`, which will provide the model with the pre-trained weights. You task is to use it to produce segmentation masks for the following image of a car.

![car image](https://assets.datacamp.com/production/repositories/6315/datasets/3a90c5f0b3523dbc13a3125bf0b6365fd3b19382/car.jpg)

```python
# Load model
model = UNet()
model.eval()

# Load and transform image
image = Image.open('car.jpg')
transform = transforms.Compose([transforms.ToTensor()])
image_tensor = transform(image).unsqueeze(0)

# Predict segmentation mask
with torch.no_grad():
    prediction = model(image_tensor).squeeze(0)

# Display mask
plt.imshow(prediction[1, :, :])
plt.show()
```

![[Pasted image 20260806095200.png]]

### Panoptic Segmentation

#### Setup up semantic masks

A common way to perform panoptic segmentation is to combine together the outputs of semantic and instance segmentation. Consider the following image of a New York street.

![street image](https://assets.datacamp.com/production/repositories/6315/datasets/8b4665801b8322842375dd7b6d40b50515fa6a2c/panopt-orig.png)

Your task is to segment it panoptically, such that each cab is identified as a separate object, while distinguishing between the asphalt and building backgrounds.

To achieve it, you will start by producing a semantic mask with a pre-trained U-Net, available to you as `UNet()`. Hopefully, it should distinguish between the two background types (but not between particular cabs)!

```python
# Instantiate the model
model = UNet()

# Produce semantic masks for the input image
with torch.no_grad():
    semantic_masks = model(image_tensor)

# Choose highest-probability class for each pixel
semantic_mask = torch.argmax(semantic_masks, dim = 1)

# Display the mask
plt.imshow(semantic_mask.squeeze(0))
plt.axis("off")
plt.show()
```

#### Overlay instance masks

Good job producing the semantic mask in the previous exercise! Now, you can overwrite it with instance masks in the locations where the objects have been identified by the instance segmentation model.

You will use the pre-trained `MaskRCNN` available in your workspace to produce instance segmentation masks. Then, you will loop over these masks and for each of them, you will overlay the parts where an object is detected with high certainty on top of the semantic mask.

```python
# Instantiate model and produce instance masks
model = MaskRCNN()
with torch.no_grad():
    instance_masks = model(image_tensor)[0]["masks"]

# Initialize panoptic mask as semantic_mask
panoptic_mask = torch.clone(semantic_mask)

# Iterate over instance masks
instance_id = 3
for mask in instance_masks:
    # Set panoptic mask to instance_id where mask > 0.5
    panoptic_mask[mask > 0.5] = instance_id
    instance_id += 1
    
# Display panoptic mask
plt.imshow(panoptic_mask.squeeze(0))
plt.axis("off")
plt.show()
```
![[Pasted image 20260806095210.png]]
## Image generation with GANs

### Introduction to GANs

GANs employ two neural networks, the Generator and the Discriminator, that are trained simultaneously through adversarial training, with the objective to create new data that is indistinguishable from real data

#### Generator

A GAN generator takes a random noise vector as input and produces a generated image. To make its architecture more reusable, you will pass both input and output shapes as parameters to the model. This way, you can use the same model with different sizes of input noise and images of  
varying shapes.

You will find `torch.nn` imported already imported for you as `nn`. You can also access a custom `gen_block()` function which returns a block of: linear layer, batch norm, and ReLU activation. You will use it as a building block for the generator.

```python
class Generator(nn.Module):
    def __init__(self, in_dim, out_dim):
        super(Generator, self).__init__()
        # Define generator block
        self.generator = nn.Sequential(
            gen_block(in_dim, 256),
            gen_block(256, 512),
            gen_block(512, 1024),
          	# Add linear layer
            nn.Linear(1024, out_dim),
            # Add activation
            nn.Sigmoid()
        )

    def forward(self, x):
      	# Pass input through generator
        return self.generator(x)
```

#### Discriminator

With the generator defined, the next step in building a GAN is to construct the discriminator. It takes the generator's output as input, and produces a binary prediction: is the input generated or real?

You will find `torch.nn` imported already imported for you as `nn`. You can also access a custom `disc_block()` function which returns a block of a linear layer followed by a LeakyReLU activation. You will use it as a building block for the discriminator.

```python
class Discriminator(nn.Module):
    def __init__(self, im_dim):
        super(Discriminator, self).__init__()
        self.disc = nn.Sequential(
            disc_block(im_dim, 1024),
            disc_block(1024, 512),
            # Define last discriminator block
            disc_block(512, 256),
            # Add a linear layer
            nn.Linear(256, 1),
        )

    def forward(self, x):
        # Define the forward method
        return self.disc(x)
```

### Deep Convolutional GAN

#### Convolutional Generator

Define a convolutional generator following the DCGAN guidelines discussed in the last video.

`torch.nn` has been pre-imported as `nn` for your convenience. Additionally, a custom function `dc_gen_block()` is available, which eturns a block of a transposed convolution, batch norm, and ReLU activation. This function serves as a foundational component for constructing the convolutional generator. You can get familiar with `dc_gen_block()`'s definition below.

```python
class DCGenerator(nn.Module):
    def __init__(self, in_dim, kernel_size=4, stride=2):
        super(DCGenerator, self).__init__()
        self.in_dim = in_dim
        self.gen = nn.Sequential(
            dc_gen_block(in_dim, 1024, kernel_size, stride),
            dc_gen_block(1024, 512, kernel_size, stride),
            # Add last generator block
            dc_gen_block(512, 256, kernel_size, stride),
            # Add transposed convolution
            nn.ConvTranspose2d(in_dim, 3, kernel_size, stride=stride),
            # Add tanh activation
            nn.Tanh()
        )

    def forward(self, x):
        x = x.view(len(x), self.in_dim, 1, 1)
        return self.gen(x)
```

#### Convolutional Discriminator

With the DCGAN's generator ready, the last step before you can proceed to training it is to define the convolutional discriminator.

`torch.nn` is imported for you under its usual alias. To build the convolutional discriminator, you will use a custom `gc_disc_block()` function which returns a block of a convolution followed by a batch norm and the leaky ReLU activation. You can inspect `dc_disc_block()`'s definition below.

```python
class DCDiscriminator(nn.Module):
    def __init__(self, kernel_size=4, stride=2):
        super(DCDiscriminator, self).__init__()
        self.disc = nn.Sequential(
          	# Add first discriminator block
            dc_disc_block(3, 512, kernel_size, stride),
            dc_disc_block(512, 1024, kernel_size, stride),
          	# Add a convolution
            nn.Conv2d(1024, 1, kernel_size, stride=stride),
        )

    def forward(self, x):
        # Pass input through sequential block
        x = disc(x)
        return x.view(len(x), -1)
```

#### Generator loss

Before you can train your GAN, you need to define loss functions for both the generator and the discriminator. You will start with the former.

Recall that the generator's job is to produce such fake images that would fool the discriminator into classifying them as real. Therefore, the generator incurs a loss if the images it generated are classified by the discriminator as fake (label `0`).

Define the `gen_loss()` function that calculates the generator loss. It takes four arguments:

- `gen`, the generator model
- `disc`, the discriminator model
- `num_images`, the number of images in batch
- `z_dim`, the size of the input random noise

```python
def gen_loss(gen, disc, criterion, num_images, z_dim):
    # Define random noise
    noise = torch.random(num_images, z_dim)
    # Generate fake image
    fake = gen(noise)
    # Get discriminator's prediction on the fake image
    disc_pred = disc(fake)
    # Compute generator loss
    criterion = nn.BCEWithLogitsLoss()
    gen_loss = criterion(disc_pred, torch.ones_like(disc_pred))
    return gen_loss
```

#### Discriminator loss

It's time to define the loss for the discriminator. Recall that the discriminator's job is to classify images either real or fake. Therefore, the generator incurs a loss if it classifies generator's  
outputs as real (label `1`) or the real images as fake (label `0`).

```python
def disc_loss(gen, disc, real, num_images, z_dim):
    criterion = nn.BCEWithLogitsLoss()
    noise = torch.randn(num_images, z_dim)
    fake = gen(noise)
    # Get discriminator's predictions for fake images
    disc_pred_fake = disc(fake)
    # Calculate the fake loss component
    fake_loss = criterion(
        disc_pred_fake,
        torch.zeros_like(disc_pred_fake)
    )
    # Get discriminator's predictions for real images
    disc_pred_real = disc(real)
    # Calculate the real loss component
    real_loss = criterion(
        disc_pred_real,
        torch.ones_like(disc_pred_real)
    )
    disc_loss = (real_loss + fake_loss) / 2
    return disc_loss
```

#### Training loop

Finally, all the hard work you put into defining the model architectures and loss functions comes to fruition: it's training time! Your job is to implement and execute the GAN training loop. Note: a `break` statement is placed after the first batch of data to avoid a long runtime.

The two optimizers, `disc_opt` and `gen_opt`, have been initialized as `Adam()` optimizers. The functions to compute the losses that you defined earlier, `gen_loss()` and `disc_loss()`, are available to you. A `dataloader` is also prepared for you.

Recall that:

- `disc_loss()`'s arguments are: `gen`, `disc`, `real`, `cur_batch_size`, `z_dim`.
- `gen_loss()`'s arguments are: `gen`, `disc`, `cur_batch_size`, `z_dim`.

```python
for epoch in range(1):
    for real in dataloader:
        cur_batch_size = len(real)
        
        disc_opt.zero_grad()
        # Calculate discriminator loss
        d_loss = disc_loss(
            gen, disc, real, cur_batch_size, z_dim = 16
        )
        # Compute gradients
        d_loss.backward()

        disc_opt.step()

        gen_opt.zero_grad()
        # Calculate generator loss
        g_loss = gen_loss(
            gen, disc, cur_batch_size, z_dim = 16
        )
        # Compute generator gradients
        g_loss.backward()
        gen_opt.step()

        print(f"Generator loss: {g_loss}")
        print(f"Discriminator loss: {d_loss}")
        break
```

### Evaluating GANs

#### Generating images

Now that you have designed and trained your GAN, it's time to evaluate the quality of the images it can generate. For a start, you will perform a visual inspection to see if the generation resemble the Pokemons at all. To do this, you will create random noise as input for the generator, pass it to the model and plot the outputs.

The Deep Convolutional Generator with trained weights is available to you as `gen`. `torch` and `matplotlib.pyplot` as `plt` are already imported for you.

```python
num_images_to_generate = 5
# Create random noise tensor
noise = torch.randn(num_images_to_generate, 16)

# Generate images
with torch.no_grad():
    fake = gen(noise)
print(f"Generated tensor shape: {fake.shape}")
    
for i in range(num_images_to_generate):
    # Slice fake to select i-th image
    image_tensor = fake[i, :, :, :]
    # Permute the image dimensions
    image_tensor_permuted = image_tensor.permute(1,2,0)
    plt.imshow(image_tensor_permuted)
    plt.show()
```

#### Fréchet Inception Distance

The visual inspection of generated images is a great start. But given they look okay, a more precise, quantitative evaluation will be helpful to understand the generator's performance. You will evaluate your GAN using the Fréchet Inception Distance, or FID.

Two tensors with fake and real images, 32 examples each, are available to you as `fake` and `real`, respectively. Use them to compute the FID!

```python
# Import FrechetInceptionDistance
from torchmetrics.image.fid import FrechetInceptionDistance

# Instantiate FID
fid = FrechetInceptionDistance(feature = 64)

# Update FID with real images
fid.update((fake * 255).to(torch.uint8), real = False)
fid.update((real * 255).to(torch.uint8), real = True)

# Compute the metric
fid_score = fid.compute()
print(fid_score)
```
