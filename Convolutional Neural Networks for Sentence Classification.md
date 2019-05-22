# Convolutional Neural Networks for Sentence Classification

### Introduction
- Word2Vec으로 학습한 vector representation을 cnn에 이용(100 billion word training)


### Model
word vector의 dimension : k
문장의 길이 : n
window size : h
라고 했을 때, convolution 연산을 표현하면  
c<sub>i</sub> = f(w*x<sub>i:i+h-1</sub> + b)

* max-over time pooling : convolution 연산에서 나온 feature map에서 max(c)를 취하는 방법(global max pooling같은 의미인듯)
* 즉 1개의 feature map에서 1개의 max feature만 남음
* filter size를 다르게 해서 여러 feature를 추출
* 그후 FC layer로 classification


### Hyperparameter and Training
- ReLU 사용
- filter size : 3, 4, 5 witt 100 feature maps each
- dropout : 0.5
- L2 regularization: 3
- Mini batch : 50
- 