# View Based Training of Convolutional Neural Networks for Plant Disease Identification

## Data Preparation

The datasets were first compiled into separate Ground and Aerial POV datasets within RoboFlow. This also allowed the annotations to be unified into one consistent format (Retinanet Keras CSV). Each class was split 80/20 training/test before exporting. The datasets were then imported into a Google Colab notebook via RF’s API and transformed to ensure they are within the expected parameters for the architectures used in this project. These transformations included ensuring the photos were within the RGB color space, resizing them to 224x224 pixels normalizing their pixel values and transforming the image data type to a Pytorch tensor. Class labels were also converted to integers to ensure compatibility. The combined dataset has different images /annotations within its splits than its POV counterparts. The classes used in this set were: Corn leaf blight, Banana Fusarium Wilt, Banana healthy, Cherry armillaria mellea, Cherry leaf healthy, Corn Gray leaf spot, Corn leaf healthy, Corn rust leaf, Peach Anarsia Lineatella, and Peach leaf healthy.

## Aerial Set

Since the hyperspectral aerial images within the cherry, peach and banana datasets were in large TIF files, they first had to be converted into a JPEG map within QGIS and manually labelled within RoboFlow based on the csv map included by the image dataset creators. Aerial images from these sets were then augmented via tiling (3x3), 90 degrees rotation (counterclockwise or clockwise), hue variation (between -15 and +15), saturation variation (between -25 and +25), blurring and random noise. This resulted in 1788 images from the cherry and peach images aerial images from the original 62 images. 
After adding in the augmented images, the aerial dataset included 9638 images with 42725 annotations. Overrepresented class annotations were limited when imported into Google Colab to ensure the training and testing datasets were as balanced as possible. Corn leaf blight was limited to 800 (training)/200 (test); Cherry armillaria mellea was limited to 800(training) / 200(test); and Peach anarsia lineatella was limited to 800 (training)/200 (test). Images with unrecognized annotations or annotations over their class limit were not trained or tested on. 

## Ground Set

The ground set required less data preparation than the aerial set. Overall, there were 4078 images with 7710 annotations. There were still some class annotation imbalances. Banana fusarium wilt was limited to 800 (training)/200(test) and peach leaf healthy was also limited to 800(training)/200(test).
Combined Set

The combined dataset featured 13716 images with 50435 annotations total. Corn leaf blight was limited to 1000 (train)/250 (test).

Training and Validation

All models were coded in python, using the Pytorch library, and trained within a Google Colab environment(on a T4 GPU High RAM runtime). The predefined architectures used for this project were AlexNet, VGG19, mobileNet V3 small, GoogleNet and ResNet50 were loaded in with their default weights. The fully connected heads were modified to classify the dataset into 10 distinct classes. Adam was used as the optimizer and Cross Entropy Loss was the loss function used for all models. A Step LR (step size= 10) was used to adjust the learning rate as validation progressed.
POV Models and Full Set Models
The POV models and the POV agnostic models were trained and validated using K-Fold validation, with k=5, for 30 epochs. For each fold the training dataset was split into training and validation sets and then put into data loaders with a batch size of 64 to ensure that not all data is loaded at once (possibly resulting in a session crash). POV models were trained and validated only on their respective image subsets. POV models were saved for later use in a combined model.
Combined Models
POV models (of the same architecture) were imported, and their weight dictionaries were isolated. These weight dictionaries were combined with a lambda weight of 0.5 and added to an empty weight dictionary. While this functions the same as averaging the model weights, the lambda combination was selected to allow for testing with different contributions from each model. In testing for these models 0.5 had the best results. This combined dictionary is then loaded into an empty model of the same predefined architecture. The new unified model head is modified to classify the images into 10 distinct classes. Ten epochs of fine-tuning training are done on the combined model to ensure the head is functioning correctly.
