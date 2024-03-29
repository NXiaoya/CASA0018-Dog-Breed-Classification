# Dog Breed Classification on mobile phone


Xiaoya Nie

Edge Impulse projects: https://studio.edgeimpulse.com/studio/196547

## Introduction
It is estimated that there are over 90 million of dogs around the world and over 75% of them are Free-range dogs (Atitwa, 2018). They can be stray dogs, wild dogs, street dogs and village dogs. To identify the breed of the dog, the traditional way is based on life experience or ask the expert. For dog shelters, it is unlikely that they could find the expert for dog breed identification. It is necessary to identify a dog's species in order to provide appropriate care and instruction or even help them to find a new home. Borwarnginn et al. (2019) compared two image processing methods for the classification of dog breeds and found that the deep learning method based on a convolutional neural network was more accurate. The objective of this project is to build a deep learning model that can accurately classify the breed of a dog in an image and deploy on mobile device. The model will be trained on a large-scale dataset of dog images, and extract dog features from the images, followed by fully connected layers to perform classification in Edge Impulse.

## Research Question
Can computer vision accurately detect the dog breed based on images input on a mobile phone?

## Application Overview
This application was created for image classification. Based on the training dataset, images for a total of 40 dog breeds were imported. This model was built within Edge Impulse for feature generation and image training before being converted to TensorFlow Lite model. By scanning the QR code generated by Edge Impulse, the phone automatically downloads and installs model package for the dog breeds classification, eliminating the need to transmit files or configuration settings manually. When the model building is complete on the phone, the user can access the page for dog breed classification. The camera captures images in real time and reduces it to 160x160 pixels. The processed images are loaded into the model. If the model recognizes one of the dog breeds in the image, breed information will be displayed below the image. If no match is found, "Unknown" will show in the text.

<img src="https://github.com/NXiaoya/CASA0018-Dog-Breed-Classification/blob/main/Images/ApplicationOverview.jpg" height="400"/>

Figure 1. Project application overview


## Data
The dataset used for this project is a 70 dog breed dataset from Kaggle (figure 2), created by the owner of a dog grooming business to help customers identify their dogs' breeds (Piosenka, 2021). This dataset includes 7946 training, 700 test, and 700 validation images, each in 224X224X3 jpg format (figure 3). Before start working on the model, the training and testing image data need to upload to the platform and label them with name of the dog breed (figure 4). Due to the limited training time of free version (20 min), the number of breeds was reduced from 70 to 40.
As this project need to do the image classification with a large number of data, transfer leaning block was chosen (figure 5). The images need to be resized. The pretrained image dataset eliminate the image distortion in this step compared with a normal image dataset. The processed image data was input into edge impulse to generate the feature of dogs.  However, it can be seen that the feature of dogs has hug overlap between each breed (figure 6). It indicates that the classes of dog breed images are not well separated. The model result might be influenced.


<img src="https://github.com/NXiaoya/CASA0018-Dog-Breed-Classification/blob/main/Images/image_dataset.png " height="400"/>

Figure 2. 70 dog breed dataset from Kaggle (Piosenka, 2021)

<img src="https://github.com/NXiaoya/CASA0018-Dog-Breed-Classification/blob/main/Images/image_dataset_dog.png " height="400"/>

Figure 3. The pretrained images (Piosenka, 2021).

<img src="https://github.com/NXiaoya/CASA0018-Dog-Breed-Classification/blob/main/Images/TrainingSetUpload.png " height="400"/>

Figure 4. Data acquisition.


<img src="https://github.com/NXiaoya/CASA0018-Dog-Breed-Classification/blob/main/Images/trandferlearningblock.png " height="400"/>

Figure 5. Training block in Edge Impulse

<img src="https://github.com/NXiaoya/CASA0018-Dog-Breed-Classification/blob/main/Images/feature.png " height="400"/>

Figure 6. Training block in Edge Impulse


## Model

The training model used for this project is MobileNet. This network is primarily based on depthwise convolutions (Howard, 2017). Compared with standard convolution method, MobileNet reduced computation by the 8 to 9 times less with a minor decrease in accuracy. Therefore, MobileNet is suitable for applications with limited computation power.
Based on the MobileNetV1, a google researcher group introduced MobileNetV2 in 2018 (Sandler et al, 2018). MobileNetV2 use inverted residual blocks with bottlenecking features. The building block for MobileNetV2 is shown in figure 7.  For the residual block of MobileNetV2, it firstly used 1* 1 convolution to raise the dimension, and then use a 3* 3 Dwise convolution, the third layer is a 1* 1 linear convolution to convert to a low dimension. 

<img src="https://github.com/NXiaoya/CASA0018-Dog-Breed-Classification/blob/main/Images/block.png " height="400"/>

Figure 7. MobileNetV2 block diagram

To find a suitable model, MobileNetV1 and MobileNetV2 were both tested. The result is shown in figure 8. With the same size image input, the test data accuracy of MobileNetV2 was around 20% higher than that of MobileNetV1.

<img src="https://github.com/NXiaoya/CASA0018-Dog-Breed-Classification/blob/main/Images/choosemodel.png " width="900"/>

Figure 8. MobileNetV2 and V1 result compare.

## Experiments
Then it comes to the fine tuning of the model, several parameters were changed to find the model with better performance. Based on MobileNet V2, the size of the image were changed first (figure 9). The loss increased and the validation accuracy decreased significantly. The performance difference for the test data is relatively small. Therefore, the image size with 160*160 pixels can provide a more accurate result. Another parameter change is this table is the alpha value. Alpha value indicates the width of the training network. In this project, the alpha value changed from 0.35 to 0.5. The accuracy of the model was higher with a higher alpha value. However, more computation power is needed for training.


<img src="https://github.com/NXiaoya/CASA0018-Dog-Breed-Classification/blob/main/Images/size.png" width="900"/>
Figure 9. Results for changing image size and alpha value

Edge Impulse also allow user to change the color depth before generating the feature. To evaluate the influence of color depth on the model testing result, MobileNetV1 was used with RGB and greyscale data input separately. (MobileNetV2 do not support grey scale image input) The accuracy dropped, and the loss increased for grey scale. It shows that the dog color and its distribution are important features for identify the dog breed. Figure 11 shows the converted grey scale images for 4 different dog breeds. The color distribution difference of grey scale is less significant for 2 dog breeds on the right than that of the RGB images.

<img src="https://github.com/NXiaoya/CASA0018-Dog-Breed-Classification/blob/main/Images/RGB.png" width="900"/>
Figure 10. Results for changing color depth

<img src="C:\Users\10596\Desktop\deep learning\report\picture\dogrgb.png" width="900"/>
Figure 11.RGB and grey scale dog images

Then I changed the learning rate to investigate the change for model performance. With a high learning rate, the model overfits quickly (figure 12). For a learning rate with 0.00005, the model accuracy slightly increased and the loss decreased. But the training process also increased. A learning rate with 0.0005 is considered to be a suitable value to find a balance between model stability and computation time.

<img src="https://github.com/NXiaoya/CASA0018-Dog-Breed-Classification/blob/main/Images/learning rate.png" width="900"/>
Figure 11. Results for changing learning rate

## Results and Observations

According to the experiments above, the model with the best result is MobileNetV2 (alpha value 0.5 and learning rate 0.0005) with 160*160 pixel RGB image data input. The accuracy for model testing results is 95.37%. The model testing perfrmance align with the repection

<img src="https://github.com/NXiaoya/CASA0018-Dog-Breed-Classification/blob/main/Images/bestperformance.png" height="900"/>

Figure 12. Results for model with best performance

After building the model, it can be deployed on mobile phone. Screenshots are shown in figure 13. The result testing on mobile phone was based on images searched in Internet. The test results were accurate at most of times. For some breeds like Pug, the testing on mobile phone could not match with the breed (figure 13).

<img src="https://github.com/NXiaoya/CASA0018-Dog-Breed-Classification/blob/main/Images/MobileTest1.png" height="400"/> <img src="https://github.com/NXiaoya/CASA0018-Dog-Breed-Classification/blob/main/Images/MobileTest2.png" height="400"/> <img src="https://github.com/NXiaoya/CASA0018-Dog-Breed-Classification/blob/main/Images/MobileTest3.png" height="400"/> 

Figure 13. Deploy the model on mobile phone

For this problem, in the detailed result for each breed in figure 12, there are three key factors. F1 score is determined by both precision and recall, for example, recall is the number of Chowchow that has been retrieved compared with the actual number of Chowchow in the dataset. And precision is the number of correct Chowchow in all Chowchow that has been retrieved. Therefore, the model shows a relatedly limited performance on retrieving images of several breeds. This is one of targets for the future improvements of the project.

During the training process, I faced several technical difficulties. Firstly, for the image dataset, there are wide range of characteristics for the dog which make it hard to generate dog features as it could have some overlap.  During the fine-tuning process, finding a balance between the best performance model with the computation power needs time to run the model and evaluate carefully.

For the improvements and future development, I want to build a self-trained image dataset instead of using the source from Kaggle. From the training process I noticed that the quality of the image dataset will influence on the model testing result. A better image dataset could prevent the image distortion and help to generate the features. Another improvement is to improve the dog breed classification model and add more dog breeds to improve the retrieve of the model and enhance the classification. As the target users are animal shelters, veterinary clinics, and dog lovers, the application also needs to identify the mixed-breed dogs. For application development, it might be good if the model can be deployed on microcontroller and build a device with camera. User do not need to scan the QR code and wait the model build on phone every time.

## Bibliography

1. Atitwa, S.C. (2018) How many dogs are there in the world?, WorldAtlas. WorldAtlas. Available at: https://www.worldatlas.com/articles/how-many-dogs-are-there-in-the-world.html (Accessed: February 21, 2023).

2. Borwarnginn, P., Thongkanchorn, K., Kanchanapreechakorn, S. and Kusakunniran, W. 'Breakthrough Conventional Based Approach for Dog Breed Classification Using CNN with Transfer Learning'. (2019) International Conference on Information Technology and Electrical Engineering (ICITEE), 10-11 Oct. 2019, 1-5.

3. Howard, A.G., Zhu, M., Chen, B., Kalenichenko, D., Wang, W., Weyand, T., Andreetto, M. and Adam, H., 2017. Mobilenets: Efficient convolutional neural networks for mobile vision applications. arXiv preprint arXiv:1704.04861.

4. Piosenka, G. (2021). 70 Dog Breeds/Image Data Set. Kaggle. Available at:  https://www.kaggle.com/datasets/gpiosenka/70-dog-breedsimage-data-set (Accessed: February 21, 2023)


5. Sandler, M., Howard, A., Zhu, M., Zhmoginov, A. and Chen, L.C., 2018. Mobilenetv2: Inverted residuals and linear bottlenecks. In Proceedings of the IEEE conference on computer vision and pattern recognition (pp. 4510-4520).

----

## Declaration of Authorship

I, AUTHORS Xiaoya Nie, confirm that the work presented in this assessment is my own. Where information has been derived from other sources, I confirm that this has been indicated in the work.


Xiaoya Nie

27th April 2023
