# YOLO-Patch-Based-Inference
This library facilitates various visualizations of inference results from YOLOv8 and YOLOv9 models, cropping with overlays, as well as a __patch-based inference algorithm enabling detection of small objects in images__. It works for both object detection and instance segmentation tasks using YOLO models.

## Installation
You can install the library via pip:

```bash
pip install patched_yolo_infer
```

</details>

## Notebooks

Interactive notebooks are provided to showcase the functionality of the library. These notebooks cover batch-inference procedures for detection, instance segmentation, inference custom visualization, and more. Each notebook is paired with a tutorial on YouTube, making it easy to learn and implement features.


                         
| Tipic | Notebook |
| ------ | ------ |
| [YOLO-Patch-Based-Inference Example](https://github.com/Koldim2001/YOLO-Patch-Based-Inference/blob/main/examples/example_patch_based_inference.ipynb) | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/2.ipynb) |
| [Example of using various functions for visualizing basic YOLOv8/v9 inference results and handling overlapping crops](https://github.com/Koldim2001/YOLO-Patch-Based-Inference/blob/main/examples/example_extra_functions.ipynb) | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/1.ipynb) |


## Usage

### 1. Patch-Based-Inference
To carry out patch-based inference of YOLO models using our library, you need to follow a sequential procedure. First, you create an instance of the MakeCropsDetectThem class, providing all desired parameters related to YOLO inference and the patch segmentation principle.<br/> Subsequently, you pass the obtained object of this class to CombineDetections, which facilitates the consolidation of all predictions from each overlapping crop, followed by intelligent suppression of duplicates. <br/>Upon completion, you receive the result, from which you can extract the desired outcome of frame processing.

The output obtained from the process includes several attributes that can be leveraged for further analysis or visualization:

1. img: This attribute contains the original image on which the inference was performed. It provides context for the detected objects.

2. confidences: This attribute holds the confidence scores associated with each detected object. These scores indicate the model's confidence level in the accuracy of its predictions.

3. boxes: These bounding boxes are represented as a list of tuples, where each tuple contains four values: (x_min, y_min, x_max, y_max). These values correspond to the coordinates of the top-left and bottom-right corners of each bounding box.

4. masks: If available, this attribute provides segmentation masks corresponding to the detected objects. These masks can be used to precisely delineate object boundaries.

5. classes_ids: This attribute contains the class IDs assigned to each detected object. These IDs correspond to specific object classes defined during the model training phase.

6. classes_names: These are the human-readable names corresponding to the class IDs. They provide semantic labels for the detected objects, making the results easier to interpret.

```python
import cv2
from patched_yolo_infer import MakeCropsDetectThem, CombineDetections

# Load the image 
img_path = 'test_image.jpg'
img = cv2.imread(img_path)

element_crops = MakeCropsDetectThem(
    image=img,
    model_path="yolov8m.pt",
    segment=False,
    shape_x=640,
    shape_y=640,
    overlap_x=50,
    overlap_y=50,
    conf=0.5,
    iou=0.7,
    resize_initial_size=True,
)
result = CombineDetections(element_crops, nms_threshold=0.05, match_metric='IOS')  

# Final Results:
img=result.image
confidences=result.filtered_confidences
boxes=result.filtered_boxes
masks=result.filtered_masks
classes_ids=result.filtered_classes_id
classes_names=result.filtered_classes_names
```


### 2. Custom inference visualization:
Visualizes custom results of object detection or segmentation on an image.

    Args:
        img (numpy.ndarray): The input image in BGR format.
        boxes (list): A list of bounding boxes in the format [x_min, y_min, x_max, y_max].
        classes_ids (list): A list of class IDs for each detection.
        confidences (list): A list of confidence scores corresponding to each bounding box. Default is an empty list.
        classes_names (list): A list of class names corresponding to the class IDs. Default is an empty list.
        masks (list): A list of masks. Default is an empty list.
        segment (bool): Whether to perform instance segmentation. Default is False.
        show_boxes (bool): Whether to show bounding boxes. Default is True.
        show_class (bool): Whether to show class labels. Default is True.
        fill_mask (bool): Whether to fill the segmented regions with color. Default is False.
        alpha (float): The transparency of filled masks. Default is 0.3.
        color_class_background (tuple): The background bgr color for class labels. Default is (0, 0, 255) (red).
        color_class_text (tuple): The text color for class labels. Default is (255, 255, 255) (white).
        thickness (int): The thickness of bounding box and text. Default is 4.
        font: The font type for class labels. Default is cv2.FONT_HERSHEY_SIMPLEX.
        font_scale (float): The scale factor for font size. Default is 1.5.
        delta_colors (int): The random seed offset for color variation. Default is 0.
        dpi (int): Final visualization size (plot is bigger when dpi is higher). Default is 150.
        random_object_colors (bool): If true, colors for each object are selected randomly. Default is False.
        show_confidences (bool): If true and show_class=True, confidences near class are visualized. Default is False.
        axis_off (bool): If true, axis is turned off in the final visualization. Default is True.
        show_classes_list (list): If empty, visualize all classes. Otherwise, visualize only classes in the list.

Example of using:
```python
from patched_yolo_infer import visualize_results

# Assuming result is an instance of the CombineDetections class
result = CombineDetections(...)  # Instance creation code here

# Visualizing the results using the visualize_results function
visualize_results(
    img=result.image,
    confidences=result.filtered_confidences,
    boxes=result.filtered_boxes,
    masks=result.filtered_masks,
    classes_ids=result.filtered_classes_id,
    classes_names=result.filtered_classes_names,
    segment=False,
)
```