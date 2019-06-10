# Image Super-Resolution Using Deep Convolutional Networks

### Introduction

* 최초로 super resolution 영역에 cnn을 적용한 논문
* 이후 sr 관련 논문은 이 논문에서 제시하는 구조를 바탕으로 함
* Super resolution 문제는 solution이 여러 개인 ill-posed 문제
* CNN을 적용함으로써 기존 SOTA인 Sparse coding보다 성능도 훨씬 앞서고 CPU에서도 돌릴 수 있을 만큼 빠른 속도를 냄


### Convolutional Neural Networks for super-resolution

* denote : Y를 low resolution image, X를 hight resolution(bicubic 으로 interpolate)이미지로 표기
* F*(Y)가 X가 되도록 F 학습 
* Y는 original image X를 downsizing한 후 bicubic interpolation으로 X와 같은 size로 만드는 것으로 보임/
*  
**steps**
1. Patch extraction and representations : input image Y로부터 overlapped patch를 추출
2. Non-linear mapping : cnn을 통해 high resolution patch 구함
3. Reconstruction : patch를 합쳐서 high resolution 이미지 생성하고 X와 비교

##### detail
1. Patch extraction and representations
표현만 patch extraction일뿐, 적당한 filter size로 convolution 연산을 하는 걸 표현한 것임.  연산 결과인  n1개의 feature map은 각 patch의 feature라고 여기는 것으로 보인다. activation은 ReLU 사용

2. Non-linear mapping
여기서는 1x1 convolution을 사용해서 다시 n2개의 feature map으로 mapping한다.  
3x3이나 5x5를 사용해도된다는건가.. 어쨌든 여기도 일반적인 conv layer

3. Recustruction
원래 채널(대부분 3)의 개수만큼 filter 수를 맞추고 convolution 연산을 한다. 

### training
* loss function은 MSE를 사용(Image restoration 지표인 PSNR을 높이는 것과 일치함..)
* momentum based SGD (마지막 레이어 는 lr을 0.0005, 나머지는 0.0004)
* weight initialization : N(0, 0.001) (bias는 0)
* training시 image를 sub-image로 random crop한 것을 사용한다(f<sub>sub</sub>)
* 모든 conv layer는 padding을 적용하지 않아, output이 이미지 사이즈보다 작아지는데, 이 때 MSE는 Ground Truth이미지의 가운데 pixel에 대해서만 계산

##### Dataset 만드는 법
* 원본이미지를 1/scale로 줄인 후 , 다시 scale만큼 늘려 사이즈를 맞춰줌. 이때 interpolation은 bicubic하는 것으로 보임.. 논문 중간에 Gaussian blur 얘기도 나오는데 이건 어디서 적용하는지 잘 모르겠음.. 코드를 참고해야할듯


### Experiment

##### training data
* baseline model 세팅
  * f<sub>1</sub> = 9, f<sub>2</sub> = 1, f<sub>3</sub> = 5, n<sub>1</sub> = 64, n<sub>2</sub> = 32
* small dataset(91장)과 Imagenet에서 가져온 395,909장 (large dataset)을 사용
* f<sub>sub</sub> = 33 / stride 14
* 91장 -> 24800장이 됨
* 기존 SOTA방법인 sparse coding 방법으로 PSNR 31.42를 기록
* upscaling facter = 3
* small data는 PSNR 32.39, large data 는 PSNR 32.52를 기록했는데, 이를 보면 large dataset이 성능향상에 큰 영향을 주는 것으로 보이지는 않는다.
* filter number를 늘리면 성능이 약간 좋아지는 것을 확인
* filter size가 baseline 모델은 (9, 1, 5)인데, 가운데 filter를 고정하고 9->11, 5->7로 바꿨을 때 성능향상이 약간 있음. 또한 1-> 3, 5로 바꾸면 성능이 유의미하게 향상되는데, 주위 정보를 활용하는 것이 좋다는 것을 보여주는 결과이다.
* layer를 추가
  * (9,1,5) 대신 (9, 1, 1, 5), (9, 3, 1, 5), (9, 5, 1, 5)를 사용했지만, 3 layer에 비해 크게 향상되는 것은 없었음.