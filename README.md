# keras_ensemble_cifar10

This repository is supported by **Huawei (HCNA-AI Certification Course)** and **Student Innovation Center of SJTU**.  
Thanks to the teachers for their contributions. 

![cifar10][1]

I just use **Keras** and **Tensorflow** to implementate all of these models and do some ensemble experiments based on [BIGBALLON's work][22].

## Requirements

- Python (3.5)
- keras (>= 2.1.5)
- tensorflow-gpu (>= 1.4.1)

## Architectures and papers


- **Vgg19 Network**
    -  [Very Deep Convolutional Networks for Large-Scale Image Recognition][4]
    -  The **1st places** in ILSVRC 2014 localization tasks
    -  The **2nd places** in ILSVRC 2014 classification tasks 
- **Residual Network** 
    -  [Deep Residual Learning for Image Recognition][5]
    -  [Identity Mappings in Deep Residual Networks][6]
    -  **CVPR 2016 Best Paper Award**
    -  **1st places** in all five main tracks:
        - ILSVRC 2015 Classification: "Ultra-deep" 152-layer nets
        - ILSVRC 2015 Detection: 16% better than 2nd
        - ILSVRC 2015 Localization: 27% better than 2nd
        - COCO Detection: 11% better than 2nd
        - COCO Segmentation: 12% better than 2nd
-  **Wide Residual Network**
    -  [Wide Residual Networks][7]
-  **ResNeXt**  
    -  [Aggregated Residual Transformations for Deep Neural Networks][8]
    -  Used in [Mask-RCNN][9]
-  **DenseNet**
    -  [Densely Connected Convolutional Networks][10]
    -  **CVPR 2017 Best Paper Award**
-  **SENet**
    - [Squeeze-and-Excitation Networks][11]  
    - **The 1st places** in ILSVRC 2017 classification tasks 



## Documents & tutorials
 
You can aslo see the [articles][14] if you can speak Chinese. 



## Accuracy of all single models

**In particular**：  
Change the batch size according to your GPU's memory.  
Modify the learning rate schedule may imporve the results of accuracy!  
Thanks to the GPU computing platform (Composed of **100 pieces of 1080Ti**) provided by the Student Innovation Center. 


| network               | GPU           | model size  | batch size | epoch | loss function  | training time | val_acc(%)  |
|:----------------------|:-------------:|:-----------:|:----------:|:-----:|:--------------:|:-------------:|:-----------:|
| Wide-resnet 28x10     | GTX1080TI x 2 |  139M       |   128      |  250  |   crossentropy |    4 h 55 min |    96.50    |
| Wide-resnet 28x10     | GTX1080TI x 2 |  139M       |   128      |  250  |   focal_loss   |    6 h 34 min |    95.50    |
| DenseNet-160x24       | GTX1080TI x 2 | 30.2M       |    64      |  250  |   crossentropy |   24 h 22 min |    95.70    |
| DenseNet-160x24       | GTX1080TI x 2 | 30.2M       |    64      |  250  |   focal_loss   |   25 h 21 min |    95.60    |
| ResNeXt-8x64d         | GTX1080TI x 2 |  142M       |   120      |  250  |   crossentropy |   26 h 07 min |    94.40    |
| ResNeXt-8x64d         | GTX1080TI x 2 |  142M       |   120      |  250  |   focal_loss   |   35 h 10 min |    94.60    |
| SENet(ResNeXt-4x64d)  | GTX1080TI x 2 | 80.2M       |   120      |  250  |   crossentropy |   25 h 38 min |    94.27    |

I didn't calculate the accuracy in the test set. As the author of keras said, every time you use feedback from your validation process to tune your model, you leak information about the validation process into the model. Repeated just a few times, this is innocuous; but done systematically over many iterations, it will eventually cause your model to overfit to the validation process (even though no model is directly trained on any of the validation data). This makes the evaluation process less reliable.


## Accuracy of all ensemble models 

**In particular**：  
I first tune in the validation set, determine the parameters of models. 

### Voting


| Models                                                                                          | test_acc(%) |
|:------------------------------------------------------------------------------------------------|:-----------:|
| DenseNet-160x24 + Wide-ResNet 28x10                                                             | 96.10       |
| DenseNet-160x24 + Wide-ResNet 28x10 + SENet(ResNeXt-4x64d)                                      | 96.38       |
| DenseNet-160x24 + Wide-ResNet 28x10 + ResNeXt-29(8x64d) with focal loss + SENet(ResNeXt-4x64d)  | 96.38       |
| DenseNet-160x24 + Wide-ResNet 28x10 + ResNeXt-29(8x64d) with focal loss                         | 96.52       |


### Weighted Mean


| Models                                                                                           | test_acc(%) |
|:-------------------------------------------------------------------------------------------------|:-----------:|
| 0.6×Wide-ResNet 28x10 + 0.4×DenseNet-160x24                                                      | 96.38       |
| 0.8×Wide-ResNet 28x10 + 0.8×DenseNet-160x24 + 0.4×ResNeXt-29(8x64d) with focal loss              | 96.53       |
| 0.9×Wide-ResNet 28x10 +0.9×DenseNet-160x24 +0.2×SENet(ResNeXt-4x64d)                             | 96.47       |
| Wide-ResNet 28x10 + DenseNet-160x24 + ResNeXt-29(8x64d) with focal loss + 0×SENet(ResNeXt-4x64d) | 96.15       |



## About Focal Loss and Cross Entropy

Reference to paper: [Focal Loss for Dense Object Detection][12]  
Code: [mutil-class focal loss implemented in keras][23] 

In addition to solving the extremely unbalanced positive-negative sample problem, focal loss can also solve the problem of easy example dominant. That's why I did the following experiment.

### Wide-resnet 28x10
| network               | GPU           | model size  | batch size | epoch | loss function  | training time | val_acc(%)  |
|:----------------------|:-------------:|:-----------:|:----------:|:-----:|:--------------:|:-------------:|:-----------:|
| Wide-resnet 28x10     | GTX1080TI x 2 |  139M       |   128      |  250  |   crossentropy |    4 h 55 min |    96.50    |
| Wide-resnet 28x10     | GTX1080TI x 2 |  139M       |   128      |  250  |   focal_loss   |    6 h 34 min |    95.50    |

### DenseNet-160x24
| network               | GPU           | model size  | batch size | epoch | loss function  | training time | val_acc(%)  |
|:----------------------|:-------------:|:-----------:|:----------:|:-----:|:--------------:|:-------------:|:-----------:|
| DenseNet-160x24       | GTX1080TI x 2 | 30.2M       |    64      |  250  |   crossentropy |   24 h 22 min |    95.70    |
| DenseNet-160x24       | GTX1080TI x 2 | 30.2M       |    64      |  250  |   focal_loss   |   25 h 21 min |    95.60    |

### ResNeXt-8x64d
| network               | GPU           | model size  | batch size | epoch | loss function  | training time | val_acc(%)  |
|:----------------------|:-------------:|:-----------:|:----------:|:-----:|:--------------:|:-------------:|:-----------:|
| ResNeXt-8x64d         | GTX1080TI x 2 |  142M       |   120      |  250  |   crossentropy |   26 h 07 min |    94.40    |
| ResNeXt-8x64d         | GTX1080TI x 2 |  142M       |   120      |  250  |   focal_loss   |   35 h 10 min |    94.60    |

We can see from the table above, focal loss improves the accuracy of Model ResNeXt-8x64d. But it reduces the accuracy of other models.


## About Ensemble Methods

### Voting
```python
    import numpy as np
    from scipy import stats
    import pandas as pd

    models =[wresnet,densenet,resnext,senet]
    labels = []
    for m in models:
        predicts = np.argmax(m.predict(x_test), axis=1)
        labels.append(predicts)

    # Ensemble with voting
    labels = np.array(labels)
    labels = np.transpose(labels, (1, 0))
    labels = stats.mode(labels, axis=-1)[0]
    labels = np.squeeze(labels)
    error = np.sum(np.not_equal(labels, y_test1)) / y_test1.shape[0]  
    print('The precision on test : ', 1-error)
```
### Weighted Mean

```python
    import numpy as np
    from scipy import stats
    import pandas as pd

    # Predict labels with models
    dense_layer_model1 = Model(inputs=wresnet.input,
                                         outputs=wresnet.get_layer('dense_1').output)
    dense_layer_model2 = Model(inputs=densenet.input,
                                         outputs=densenet.get_layer('dense_1').output)
    dense_layer_model3 = Model(inputs=resnext.input,
                                         outputs=resnext.get_layer('dense_1').output)

    dense_output1 = dense_layer_model1.predict(x_val)
    dense_output2 = dense_layer_model2.predict(x_val)
    dense_output3 = dense_layer_model3.predict(x_val)

    best_error = 888
    best_renpin1 = 666
    best_renpin2 = 999

    for renpin1 in [0.0, 0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]:  
        for renpin2 in [0.0, 0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]:  
            ams = (renpin1)*dense_output1+(renpin2)*dense_output2+(2-renpin1-renpin2)*dense_output3
            predicts = np.argmax(ams, axis=1)
            error = np.sum(np.not_equal(predicts, y_val1)) / y_val1.shape[0] 
            print(" Precision: {} , renpin1: {} , renpin2: {}".format(1-error, renpin1, renpin2))
            if error < best_error:
                best_error = error
                best_renpin1 = renpin1
                best_renpin2 = renpin2
    print("====================================================")            
    print("Best precision: {} , renpin1:  {} , renpin2: {} ".format(1-best_error, best_renpin1, best_renpin2))
    print("====================================================")
    test_output1 = dense_layer_model1.predict(x_test)
    test_output2 = dense_layer_model2.predict(x_test)
    test_output3 = dense_layer_model3.predict(x_test)
    ams1 = (best_renpin1)*test_output1+(best_renpin2)*test_output2+(2-best_renpin1-best_renpin2)*test_output3
    predicts1 = np.argmax(ams1, axis=1)
    error1 = np.sum(np.not_equal(predicts1, y_test1)) / y_test1.shape[0] 
    print("Precision on test: {} , renpin1:  {} , renpin2: {} ".format(1-error1, best_renpin1, best_renpin2))
```

## About [Multiple GPUs Training][19] 

Since the latest version of Keras is already supported ``keras.utils.multi_gpu_model``, so you can simply use the following code to train your model with multiple GPUs:

```python
from keras.utils import multi_gpu_model
from keras.applications.resnet50 import ResNet50

model = ResNet50()

# Replicates `model` on 8 GPUs.
parallel_model = multi_gpu_model(model, gpus=8)
parallel_model.compile(loss='categorical_crossentropy',optimizer='adam')

# This `fit` call will be distributed on 8 GPUs.
# Since the batch size is 256, each GPU will process 32 samples.
parallel_model.fit(x, y, epochs=20, batch_size=256)
```

## About [Transfer Learning][28]

Keras Applications are deep learning models that are made available alongside pre-trained weights. These models can be used for prediction, feature extraction, and fine-tuning.

### Fine-tune InceptionV3 on CIFAR-10
```python
    x_train, y_train, x_val, y_val, x_test, y_test = get_CIFAR10_data()
    
    y_train = keras.utils.to_categorical(y_train, num_classes)
    y_val = keras.utils.to_categorical(y_val, num_classes)
    y_test  = keras.utils.to_categorical(y_test, num_classes)
    x_train = x_train.astype('float32')
    x_val = x_val.astype('float32')
    x_test  = x_test.astype('float32')
    
    # - mean / std
    for i in range(3):
        x_train[:,:,:,i] = (x_train[:,:,:,i] - mean[i]) / std[i]
        x_test[:,:,:,i] = (x_test[:,:,:,i] - mean[i]) / std[i]
        x_val[:,:,:,i] = (x_val[:,:,:,i] - mean[i]) / std[i]


    print('Train data shape before: ', x_train.shape)
    print('Validation data shape before: ', x_val.shape)
    print('Test data shape before: ', x_test.shape)
    print('Type train: ', type(x_train))
    
    x_train = tf.image.resize_images(x_train, [96, 96], method=0).eval(session = sess)
    x_test = tf.image.resize_images(x_test, [96, 96], method=0).eval(session = sess)
    x_val = tf.image.resize_images(x_val, [96, 96], method=0).eval(session = sess)
    
    print('Train data shape: ', x_train.shape)
    print('Validation data shape: ', x_val.shape)
    print('Test data shape: ', x_test.shape)
    print('Type train: ', type(x_train))
    # setting input pic
    input_img = Input(shape=(96, 96, 3)) 
    # create the base pre-trained model
    base_model = InceptionV3(input_tensor=input_img, weights='imagenet', include_top=False)

    # add a global spatial average pooling layer
    x = base_model.output
    x = GlobalAveragePooling2D()(x)
    # let's add a fully-connected layer
    x = Dense(1024, activation='relu')(x)
    # and a logistic layer -- let's say we have 200 classes
    predictions = Dense(10, activation='softmax')(x)

    # this is the model we will train
    model = Model(inputs=base_model.input, outputs=predictions)

    print(model.summary())

    # first: train only the top layers (which were randomly initialized)
    # i.e. freeze all convolutional InceptionV3 layers
    for layer in base_model.layers:
        layer.trainable = False

    # set optimizer
    parallel_model = multi_gpu_model(model, gpus=2)
    parallel_model.compile(loss='categorical_crossentropy', optimizer='rmsprop', metrics=['accuracy'])
   

    # set callback
    tb_cb     = TensorBoard(log_dir='./Inception/', histogram_freq=0)                                   # tensorboard log
    # change_lr = LearningRateScheduler(scheduler)                                                    # learning rate scheduler
    # ckpt      = ModelCheckpoint('./ckpt_inception.h5', save_best_only=True, mode='auto', period=1)    # checkpoint 
    cbks      = [tb_cb]                   

    # set data augmentation
    print('Using real-time data augmentation.')

    datagen   = ImageDataGenerator(horizontal_flip=True,
            width_shift_range=0.125,height_shift_range=0.125,fill_mode='reflect')
    datagen.fit(x_train)

    # start training
    start = time.time()
    parallel_model.fit_generator(datagen.flow(x_train, y_train,batch_size=batch_size), steps_per_epoch=iterations, epochs=epochs, callbacks=cbks,validation_data=(x_val, y_val))

    loss, accuracy = parallel_model.evaluate(x_test,y_test)
    print('\ntest loss',loss)
    print('accuracy',accuracy)
    end = time.time()
    print('transfer learning time',end-start)  
    model.save('transfer_inceptionV3.h5')


    # let's visualize layer names and layer indices to see how many layers
    # we should freeze:
    for i, layer in enumerate(base_model.layers):
       print(i, layer.name)

    # we chose to train the top 2 inception blocks, i.e. we will freeze
    # the first 249 layers and unfreeze the rest:
    for layer in model.layers[:249]:
       layer.trainable = False
    for layer in model.layers[249:]:
       layer.trainable = True

    # set optimizer
    # we need to recompile the model for these modifications to take effect
    # we use SGD with a low learning rate

    sgd = optimizers.SGD(lr=0.0001, momentum=0.9, nesterov=True)
    parallel_model = multi_gpu_model(model, gpus=2)
    parallel_model.compile(loss='categorical_crossentropy', optimizer=sgd, metrics=['accuracy'])
   

    # set callback
    tb_cb     = TensorBoard(log_dir='./Inception_finetune/', histogram_freq=0)                                   # tensorboard log
    cbks      = [tb_cb]                   

    # set data augmentation
    print('Using real-time data augmentation.')

    datagen   = ImageDataGenerator(horizontal_flip=True,
            width_shift_range=0.125,height_shift_range=0.125,fill_mode='reflect')
    datagen.fit(x_train)

    # start training
    start = time.time()
    parallel_model.fit_generator(datagen.flow(x_train, y_train,batch_size=batch_size), steps_per_epoch=iterations, epochs=epochs1, callbacks=cbks,validation_data=(x_val, y_val))

    loss, accuracy = parallel_model.evaluate(x_test,y_test)
    print('\ntest loss',loss)
    print('accuracy',accuracy)
    end = time.time()
    print('fine tune time',end-start)  
    model.save('finetune_inceptionV3.h5')
```

Because the input size for InceptionV3 should be no smaller than 75×75, I resize the pictures from 32×32 to 96×96.


| Models                                                                                           | test_acc(%)    |
|:-------------------------------------------------------------------------------------------------|:--------------:|
| VGG19 (Pre-trained on ImageNet)                                                                  | 75.24          |
| InceptionV3 (Pre-trained on ImageNet)                                                            | No time to run |


Frankly, I didn't get good results because I didn't have enough time to fine tune.

## About Hard Examples

Here are some images that our model does not correctly predict.

![cifar10][29]

## About Cutout & AutoAugment

-  **Model of the second-place team (Test acc: 97.1%)**
    - Reference to paper: [Improved Regularization of Convolutional Neural Networks with Cutout][24]  
    - Code: [Cutout (Pytorch)][26]



-  **Model of the first-place team (Test acc: 97.7%)**
    - Reference to paper: [AutoAugment: Learning Augmentation Policies from Data][25]  
    - Code: [Autoaugment (Tensorflow)][27]
    
    
    
   
   
## Contributors

<a href="https://github.com/wangmin0199"><img src="https://avatars1.githubusercontent.com/u/43812719?s=460&v=4" height="66px" width="66px"></a>
<a href="https://github.com/Chenzf2018"><img src="https://avatars2.githubusercontent.com/u/40626787?s=400&v=4"  height="66px" width="66px"></a>
 

Please feel free to contact me if you have any questions! 


  [1]: ./5.Others/picture/cf10.png
  [2]: http://yann.lecun.com/exdb/lenet/
  [3]: https://arxiv.org/abs/1312.4400
  [4]: https://arxiv.org/abs/1409.1556
  [5]: https://arxiv.org/abs/1512.03385
  [6]: https://arxiv.org/abs/1603.05027
  [7]: https://arxiv.org/abs/1605.07146
  [8]: https://arxiv.org/abs/1611.05431
  [9]: https://arxiv.org/abs/1703.06870
  [10]: https://arxiv.org/abs/1608.06993
  [11]: https://arxiv.org/abs/1709.01507
  [12]: https://arxiv.org/abs/1708.02002
  [13]: https://github.com/BIGBALLON/cifar-10-cnn/issues/3
  [14]: https://zhuanlan.zhihu.com/p/52508690
  [15]: http://lamda.nju.edu.cn/weixs/project/CNNTricks/CNNTricks.html
  [16]: http://lamda.nju.edu.cn/weixs/
  [17]: ./htd
  [18]: https://github.com/BIGBALLON/HTD
  [19]: https://keras.io/getting-started/faq/#how-can-i-run-a-keras-model-on-multiple-gpus
  [20]: https://github.com/liuzhuang13/DenseNet
  [21]: https://github.com/prlz77/ResNeXt.pytorch
  [22]: https://github.com/wangmin0199
  [23]: https://github.com/maozezhong/focal_loss_multi_class
  [24]: https://arxiv.org/abs/1708.04552
  [25]: https://arxiv.org/abs/1805.09501
  [26]: https://github.com/uoguelph-mlrg/Cutout
  [27]: https://github.com/tensorflow/models/tree/master/research/autoaugment
  [28]: https://keras.io/applications/#models-for-image-classification-with-weights-trained-on-imagenet
  [29]: ./5.Others/picture/error_pic1.png
