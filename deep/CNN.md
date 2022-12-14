# 합성곱 신경망(CNN, Convolution Neural Network)

## CNN 배경 
1. 완전 연결 네트워크의 문제점으로부터 시작
2. 매개변수의 폭발적인 증가
3. 공간 추론의 부족
4. 픽셀 사이의 근접성 개념이 완전 연결 계층에서는 손실됨
5. 합성곱 계층은 입력 이미지가 커져도 튜닝해야 할 매개변수 개수에 영향을 주지 않음
6. 또한 그 어떠한 이미지에도 그 차원 수와 상관없이 적용될 수 있음

## CNN 특징
1. 이미지 처리에 탁월한 성능을 보이는 신경망
   1. 컴퓨터 비전 분야 주로 활용 / 특징 추출에 뛰어난 성능
   2. 컨볼루션 레이어+풀링 레이어를 반복하여 구성
   3. 합성곱연산을 사용하여 이미지의 픽셀을 주변의 픽셀의 조합으로 대체
   4. 학습된 패턴은 평행이동불변성 가짐
   5. 이미지의 오른쪽 아래 모서리에서 어떤 패턴 학습 시 다른 곳에서도 이 패턴을 인식할 수 있다.
   6. 적은 수의 훈련 샘플 사용해서 일반화 능력가진 표현 학습 가능
   7. 근본적으로 우리가 보는 세상은 평행 이동으로 인해 다르게 인식되지 않는다.
   8. 공간적 계층 구조를 학습
   9. 1번째 합성곱 층이 에지 같은 작은 지역 패턴 학습, 2번째가 1번째 층의 특성으로 구성된 더 큰 패턴 학습
   10. 매우 복잡하고 추상적인 시각적 개념을 효과적으로 학습 가능
   11. 근복적으로 우리가 보는 세상은 공각적 계층 구조를 가지고 있다.
   12. 3차원 합성곱에서 입력 데이터의 채널 수와 필터의 채널수가 같아야 한다.
2. 이미지는 3차원 텐서(높이(세로 방향 픽셀수), 너비(가로 방향), 채널(색 성분))로 구성
3. 합성곱 연산 (Convolution operation)
   1. 필터(=커널)
      1. 커널이라고도 하며 흔히 사진 어플에서 사용하는 '이미지 필터'와 비슷한 개념
      2. 입력 이미지와 출력 이미지의 사이즈 결정하기 위해 사용(사용자가 결정)
      3. 필터의 사이즈는 '거의 항상 홀수'(3X3 or 5X5). 짝수이면 패딩이 비대칭이 되어버림
      4. 왼쪽, 오른쪽을 다르게 주어야한다.
      5. 필터의 대응하는 원소끼리 곱하고 그 합을 구한다.
      6. 중심위치가 존재, 즉 구별된 하나의 픽셀(중심 픽셀)이 존재
      7. 필터의 학습 파라미터 개수는 입력 데이터의 크기와 상관없이 일정, 따라서 과적합을 방지할 수 있음
   2. 채널
      1. 컬러로 된 이미지 처리하기 위해서는 3개의 channel 필요 / 흑백은 channel 1개로 처리
   3. feature map: 입력으로부터 커널을 사용하여 합성곱 연산을 통해 나온 결과
      1. 2개의 공간 축(높이,너비) + 깊이 축(채널)
      2. 깊이 축에 있는 각 차원은 하나의 특성, output은 입력에 대한 필터 응답을 나타내는 2D 공간상의 맵
      3. 입력 특성맵에서 작은 패치들을 추출하고 이런 모든 패치에 같은 변환을 적용하여 출력 특성맵 생성
      4. 특성맵 다운샘플링 하는 이유
         1. 처리할 특성 맵의 가중치 개수 줄이기 위해
         2. 연속적인 합성곱 층이 점점 커진 윈도우를 통해 바라보도록 만들어 필터의 공간적 계층 구조 구성
   4. 스트라이드: 두 번의 연속적인 윈도우 사이의 거리 / 필터를 적용하는 간격(이동범위)
      1. stride=2일 경우, 한칸씩 건너뛰면서 Filte 적용하여 layer의 데이터 수가 감소(2의 배수로 downsampling)
   5. 패딩
      1. 이미지 데이터의 축소 방지: CNN과정에서 위와같이 여러번의 계산을 해야하는데 초반부터 이미지가 너무 작아져 버리면 더 깊게 학습시킬 데이터가 부족해지는 현상발생
      2. Edge pixel data를 충분히 활용하기 위해 
      3. 입력과 동일한 높이와 너비를 가진 출력 특성 맵 얻고 싶을 경우 사용
      4. 입력 데이터의 주변을 특정 값으로 채우는 기법. 주로 0으로 많이 채움(zero padding)
      5. valid(패딩을 주지 않음) 와 same(Output image크기가 Input image의 크기와 동일)
      6. 패딩의 크기는 (k-1)/2 (단, stride=1)
4. Pooling Layer
   1. Pooling: 일정 영역의 정보를 축약하는 역할, 모델의 파라미터 개수를 줄여주어 연산속도 상승
      1. 모델이 물체의 주요한 특성을 학습하도록 해주며 컨볼루션 신경망이 이동 불변성 특성을 가지게 해줌
      2. MaxPooling: 각 픽셀에서 최댓값을 뽑는 과정 / 입력을 조각으로 나누어(겹쳐지지 않도록) 각 조각에서 가장 큰 값 
      3. Average Pooling: 각 픽셀에서 평균값을 뽑아내는 과정
      4. Flatten: 2/3차원 행렬 구조를 1차원의 Vector로 변환하는 과정
   2. Fully_connected: 이전 계층의 모든 노드가 다음 계층의 모든 노드에 연결된 계층
   3. subsampling: 이미지를 구성하는 픽셀들이 인접한 픽셀들끼리는 비슷한 정보를 가진다는 특성에 기반
      1. 과대적합 방지, 효율적 계산하게 도와준다.
      2. (스트라이드 없는 합성곱) 조밀한 특성맵을 만들고 그 다음 작은 패치에 대해 최대로 활성화된 특성 고르는 것



## CNN 다양한 네트워크
![image](https://user-images.githubusercontent.com/53258777/206835360-57d5ebc5-c92a-400d-9924-eec5dde62bb0.png)

### LeNet-5
1. LeNet-5는 복잡하지 않은 망을 이용하여 (당시 기준)높은 성능을 보여주었을 뿐만 아니라 Convolutional layer와 pooling의 조합을 반복하는 현대적인 CNN 구조를 제안했다는 점에서 의미가 있는 모델
2. 3개의 convolution layers, 2개의 subsampling, 1층의 full-connected, output layer 구성
3. tanh 활성화 함수 사용

![image](https://user-images.githubusercontent.com/53258777/206830876-5249c2e1-b970-4392-b4dc-c62b5bd838d9.png)

### Visual Geometry Group Net(VGGNet)
1. 네트워크의 깊이를 깊게 만드는 것이 성능에 어떤 영향을 미치는지 확인하고자 연구 시작
2. 망이 깊어지면 출력단에서 하나의 픽셀이 담고 있는 정보의 양이 많아지며 훨씬 복잡한 문제도 해결 가능
    1. 망이 깊어지면 문제점: 자유 파라미터의 수가 증가하게 되면서 overfitting 발생 가능성 높아지고 연산량의 증가로 인한 학습 시간이 오래 걸린다.
3. 활성화 함수로 ReLU 사용, Dropout 적용
4. 합성곱과 풀링 계층으로 구성된 블록과 분류를 위한 완전 연결계층으로 결합된 전형적인 구조
5. 인위적으로 데이터셋을 늘림
6. 이미지 변환, 좌우 반전 등의 변환을 시도
7. 몇 개의 합성곱 계층과 최대 풀링 계층이 따르는 5개의 블록과 3개의 완전 연결계층으로 구성
8. 모든 합성곱과 최대 풀링 계층에 padding='SAME' 적용
9. 합성곱 계층에는 stride=1, 활성화 함수로 ReLU 사용
10. 특성맵 깊이를 증가 시킴
11. 척도 변경을 통한 데이터 보강(Data Augmentation)
12. 3x3 커널을 갖는 두 합성곱 계층을 쌓은 스택이 5x5 커널을 갖는 하나의 합성곱 계층과 동일한 수용영역(ERF)을 가짐
13. 11X11 사이즈의 필터 크기를 가지는 AlexNet과 비교하여 더 작은 합성곱 계층을 더 많이 포함해 더 큰 ERF를 얻음
14. 이와 같이 합성곱 계층의 개수가 많아지면 매개변수 개수를 줄이고 비선형성을 증가시킴
15. VGG-19 아키텍쳐는 VGG-16에 3개의 합성곱 계층을 추가
#### VGG 16
```
from tensorflow.keras.applications import VGG16
vgg_net=VGG16(include_top=True, weights='imagenet',
              input_tensor=None, input_shape=None,
              pooling=None, classes=1000)
```
![image](https://user-images.githubusercontent.com/53258777/206831278-33e1c066-eae1-4696-981b-1ebd56e3262a.png)

![image](https://user-images.githubusercontent.com/53258777/206831311-0a976d2b-bc4d-4477-adff-ffb7b0609315.png)

#### VGG 19

```
from tensorflow.keras.applications import VGG19
vgg_net_19=VGG19(include_top=True, weights='imagenet',
              input_tensor=None, input_shape=None,
              pooling=None, classes=1000)
```
![image](https://user-images.githubusercontent.com/53258777/206831381-2ba54f8e-ef15-400d-9f5a-19b0878f2cb5.png)

### GoogLeNet
1. 2014 ILSVRC에서 VGG19를 이기고 우승을 차지한 네트워크
2. 인셉션 블록이라는 개념을 도입하여 인셉션 네트워크라고도 불림
3. VGG19보다 좀 더 깊은 22층으로 구성 / 구글이 개발에 참여
4. 1X1 convolution을 사용하여 특성맵의 개수를 줄이는 목적으로 사용 > 연산량 감소 > 네트워크를 더 깊이 만들어준다.
5. Inception을 사용하여 좀 더 다양한 종류의 특성을 도출
6. global average pooling은 전 층에서 산출된 특성맵들을 각각 평균낸 것을 이어서 1차원 벡터로 만들어준다. 그래야 최종적으로 이미지 분류를 위한 softmax층을 연결해줄 수 있다. > 가중치의 개수를 상당히 없애줄 수 있다.
7. gradient vanishing 문제를 극복하기 위해 중간에 두 개의 auxiliary classifier(훈련시에만 사용) 배치
8. 완전 연결 계층 대신 풀링 계층 사용 / 중간 소실로 경사 소실 문제 해결

![image](https://user-images.githubusercontent.com/53258777/206835904-697d7f66-1357-476b-97e1-387fb235ee82.png)

![image](https://user-images.githubusercontent.com/53258777/206835894-64b8bd8d-8696-46af-bfee-1e71633d901b.png)
```
from keras.applications import inception_v3
model = inceotion_v3.InceptionV3(weights='imagenet', include_top=True)
```

### ResNet
1. 2015 ILSVRC 우승을 차지한 네트워크 / 마이크로소프트에서 개발
2. ResNet 개발자들은 망을 무조건 깊게만 하면 성능이 더 좋아질까?의 의문을 가지고 테스트한 결과 깊게 한다고 무조건 좋은 것이 아닌 뭔가 새로운 방법이 있어야 망을 깊게 만드는 효과를 볼 수 있다는 것을 깨달음
3. Residual Black의 출현: 입력값을 출력값에 더해줄 수 있도록 지름길을 하나 생성
4. VGG19의 구조를 뼈대 + convolution layers 추가 + shortcuts 추가
5. shortcut을 연결해서 residual을 최소가 되게 학습한 효과 + 깊은 구조일수록 성능이 좋다.

![image](https://user-images.githubusercontent.com/53258777/206835933-41f4dea8-4e07-4ce7-bd8a-544456aaed58.png)
