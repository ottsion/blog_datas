---
title: "keras中实现多任务多loss学习"
date: 2020-05-26T16:58:24+08:00
draft: false
author: "caicai"
tags: ["ai", "keras", "loss"]
categories: ["AI"]
---





核心代码如下：

```python
# create the base pre-trained model
input_tensor = Input(shape=(299, 299, 3))
base_model = Xception(include_top=True, weights='imagenet', input_tensor=None, input_shape=None)
plot_model(base_model, to_file='xception_model.png')
base_model.layers.pop()
base_model.outputs = [base_model.layers[-1].output]
base_model.layers[-1].outbound_nodes = []
base_model.output_layers = [base_model.layers[-1]]

feature = base_model
img1 = Input(shape=(299, 299, 3), name='img_1')
img2 = Input(shape=(299, 299, 3), name='img_2')

feature1 = feature(img1)
feature2 = feature(img2)

# Three loss functions
category_predict1 = Dense(100, activation='softmax', name='ctg_out_1')(
    Dropout(0.5)(feature1)
)
category_predict2 = Dense(100, activation='softmax', name='ctg_out_2')(
    Dropout(0.5)(feature2)
)
dis = Lambda(eucl_dist, name='square')([feature1, feature2])
judge = Dense(2, activation='softmax', name='bin_out')(dis)
model = Model(inputs=[img1, img2], outputs=[category_predict1, category_predict2, judge])
model.compile(optimizer=SGD(lr=0.0001, momentum=0.9),
              loss={
                  'ctg_out_1': 'categorical_crossentropy',
                  'ctg_out_2': 'categorical_crossentropy',
                  'bin_out': 'categorical_crossentropy'},
              loss_weights={
                  'ctg_out_1': 1.,
                  'ctg_out_2': 1.,
                  'bin_out': 0.5
              },
              metrics=['accuracy'])
```

