# Data Preparation
The datasets were first compiled into separate Ground and Aerial POV datasets within RoboFlow. Most datasets were already annotated or annotation instructions. 
RoboFlow also allowed the annotations to be unified into one consistent format (Retinanet Keras CSV). Each class was split 80/20 training/test before exporting. 
Before importing into the coding workspace, the necessary libraries must be imported. (see **library_import.py**) 
The datasets were then imported into a Google Colab notebook via RF’s API and transformed to ensure they are within the expected parameters for the architectures used in this project. (See **aerial_dataset_roboflow_importer.py** for example) 

These transformations included ensuring the photos were within the RGB color space, resizing them to 224x224 pixels normalizing their pixel values and transforming the image data type to a Pytorch tensor. ( See **transforms.py**)

Class labels were also converted to integers to ensure compatibility. (see **CustomDataset_importer.py** for CustomDataset class used for importing)

The combined dataset has different images /annotations within its splits than its POV counterparts.
The classes used in this set were: Corn leaf blight, Banana Fusarium Wilt, Banana healthy, Cherry armillaria mellea, Cherry leaf healthy, Corn Gray leaf spot, Corn leaf healthy, Corn rust leaf, Peach Anarsia Lineatella, and Peach leaf healthy.

## Aerial Set
Since the multispectral aerial images within the cherry, peach and banana datasets were in large TIF files, they first had to be converted into a JPEG map within QGIS and manually labelled within RoboFlow based on the csv map included by the image dataset creators.
Aerial images from these sets were then augmented via tiling (3x3), 90 degrees rotation (counterclockwise or clockwise), hue variation (between -15 and +15), saturation variation (between -25 and +25), blurring and random noise. This resulted in 1788 images from the cherry and peach images aerial images from the original 62 images. After adding in the augmented images, the aerial dataset included 9638 images with 42725 annotations.

Overrepresented class annotations were limited when imported into Google Colab to ensure the training and testing datasets were as balanced as possible. 
Corn leaf blight was limited to 800 (training)/200 (test); Cherry armillaria mellea was limited to 800(training) / 200(test); and Peach anarsia lineatella was limited to 800 (training)/200 (test). (see **aerial_train_and_test_customDataset.py**)
Images with unrecognized annotations or annotations over their class limit were not trained or tested on.

## Ground Set
The ground set required less data preparation than the aerial set. Overall, there were 4078 images with 7710 annotations. There were still some class annotation imbalances.
Banana fusarium wilt was limited to 800 (training)/200(test) and peach leaf healthy was also limited to 800(training)/200(test). (see **ground_train_and_test_customDataset.py**)

## Combined Set
The combined dataset featured 13716 images with 50435 annotations total. Corn leaf blight was limited to 1000 (train)/250 (test). (see **full_dataset_customDataset.py**)


## Data Loading
Training datasets are further split into training and validition subsets for use in k-fold cross validation for most models. This is covered in **training_validation_loop.py**. The loaders change with each fold in that loop.

K-fold cross validation does not take place before testing in the POV Combined models and can be passed into a dataLoader just once. Testing datasets can be passed into a dataLoader right after importing the dataset. (see **sample_loader.py** for and example of train and test dataLoaders) 

