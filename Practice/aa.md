# 🌱 Code Practice

예제 실습 중 나름대로 정리해보고 싶은 것들, 해보고 싶은 것들을 실습해보고 기록해 놓는 곳입니다.


## cat_vs_dog(mini_dataset)
### 모델 7가지
- 비교할 모델 7가지
  - VGG16, VGG19, Inception_v3(GoogleNet), ResNet, Xception, MobileNet, DenseNet
- 비교할 것
  - [ train loss - validation loss / train accaurcy - validation accaurcy ] 그래프<br>
  - confusion metrics
- EarlyStopping callbacks는 사용(단, 동일 설정)
- 모델을 불러와 추가할 모델 코드/compile 설정은 아래와 동일하다

```python
inputs = keras.Input(shape=(180, 180, 3))
x = data_augmentation(inputs)
x = keras.applications.vgg19.preprocess_input(x)
x = '불러온 모델'(x)
x = layers.Flatten()(x)
x = layers.Dense(256)(x)
x = layers.Dropout(0.5)(x)
outputs = layers.Dense(1, activation='sigmoid')(x)
'새로 생성된 모델 명' = keras.Model(inputs, outputs)


'새로 생성된 모델 명'.compile(loss='binary_crossentropy',
              optimizer='rmsprop',
              metrics='acc')
```

### 결과
큰 차이없는 결과가 나왔다.<br>
작은 데이터를 사용해서 그럴 수도 있다는 생각이 들어 추후 더 큰 데이터로 모델끼리의 비교를 해보고 싶어진다.
