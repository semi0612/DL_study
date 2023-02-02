# YOLOv5 brief summary
* [참고 github](https://github.com/ultralytics/yolov5/issues/6998)

## Model Structure

<p align="center">
  <img src="https://user-images.githubusercontent.com/51469989/216215978-0eedc35c-313f-4e22-86a6-7bcd04a33d23.png" width="75%">
</p>

입력된 이미지가 low level 에서 high level이 되고, classification을 위해 trainable classifier을 거친다. 이전에 많이 사용되었던 yolov3는 FPS는 높지만 mAP자체는 비교적 낮게 측정되는 모델이였고, 그 두가지 측면을 끌어올린게 yolov5이다.

(여담이지만 yolov1~3의 개발자와 yolov5의 개발자는 다른 사람이다)


### Characteristics
yolov5의 특징이라고 하면 Yolov5s(=small), Yolov5m(=medium), YOlov5l(=large), Yolov5x(=xlarge) 와 같이 크기별로 나눠서 사용이 가능하다는 것이다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/51469989/216218596-7351c43e-6e36-4dfb-ac98-407c19620e1a.png">
</p>

정확도와 속도는 아직 같이 잡을 수 없나보다. 속도면에서는 모델의 크기가 작을수록, 정확도 면에서는 모델의 크기가 클수록 좋은 성능을 보이고 있다. YOLOv5는 이전의 YOLO시리즈 보다는 확실히 속도면에서 빠른 모습을 보인다는데, 이는 backbone과 head 차이라고 한다.

### Backbone
이미지로부터 Feature Map을 추출하는 부분. CSPNet을 사용한다. YOLOv4의 백본과 유사하며, 크기별로 나눠진 (s, m, l, x) 종류가 backbone의 종류라고 볼 수 있다.
<br>

CSPNet은 Cross Stage Partial Network라는 뜻이다. gradient의 다양성을 시작과 끝 단을 통합하는 과정을 통해 줄이면서 optimaization 과정에서의 gradients의 정보중복을 줄이고, 따라서 연산량을 줄일(완화시킬) 수 있는 Network라고 소개되어있다. <sub> [참고한 CSPNET 논문 리뷰](https://keyog.tistory.com/30) </sub>

#### Feature Extractor
- New CSP-Darknet 53
    - CSP : 백본의 종류
    - Darknet : DNN들을 학습시키고 실행시킬 수 있는 틀(framework)
    - composed of multi CNN and C3, SPPF

`<C3>` simplified variant of CSP blocks

<p align="center">
  <img src="https://user-images.githubusercontent.com/51469989/216240792-f199d4f5-78dc-4d21-844d-110af2660bad.png">
</p>

- ConvBNSiLU(Convolution + Batch Norm + SiLu activation)
    - convolutional layer followed by a batch normalization layer
    - batch normalization = 학습 과정에서 각 배치 단위 별로 데이터가 다양한 분포를 가지더라도 각 배치별로 평균과 분산을 이용해 정규화하는 것을 뜻 함
    - activation layer : sigmoid
    - SiLu(Sigmoid-Weighted Linear Units) : Sigmoid Function의 변종. 강화학습을 기반으로 하는 approximation function(근사함수?)로서, sigmoid에 입력값을 곱한 값으로 계산이 진행된다.
- BottleNeck1(depth(param) controls # of sequential repetition of the Bottleneck blocks)<br>
    → residual network(잔차 네트워크)가 50층 이상 깊어지면 bottle nect 구조를 사용하는데, 이 구조는 어떤 구조이며 왜 적용하는가?
    
<p align="center">
  <img src="https://user-images.githubusercontent.com/51469989/216242210-69124e33-2601-4e33-a245-1b954622e62b.png">
</p>

좌측의 Plain Net 구조가 Exponentially low convergence rate(기하급수적으로 낮은 수렴률)을 가지기 때문에 Deep 한 처리에는 어울리지 않으며, 겸사겸사 계산 수를 효과적으로 줄일 수도 있기 때문에 bottle nect 구조를 사용한다.

- ConvBNSiLU(2) + residual connection : 잔차연결(residual connection)은 서브층의 입력과 출력을 더해 차원을 같게 하여 둘의 연산을 가능하도록 하는 것

`<SPPF>` last CNN block : faster variant of the SPP layer
 
<p align="center">
  <img src="https://user-images.githubusercontent.com/51469989/216242971-f0a8dcc3-1d81-47ee-a2e4-773746c7903a.png">
</p>

- SPPF는 원래의 SPP레이어와 수학적인 면에서는 동일하지만 속도면에서는 더 빠르다.
- 고정된 크기를 출력하기 때문에 초기화 목적으로는 사용하지 않는다. 대신 Max Pooling 및 concat 을 통해 만들어진 것을 활용할 때 사용.
  - input : C3 레이어의 출력, 도면블록은 동일하지만 배열이 다르다
  - maxpool : 넓은 영역에서 max 값을 잡고 ConvBNSiLU과 연결
  - 연결된 출력이 다른  ConvBNSiLU 층을 통과하며 출력을 뱉어낸다.

`<Neck>` combines info from layers of different depths

<p align="center">
  <img src="https://user-images.githubusercontent.com/51469989/216245376-9f1d99f2-3936-41e2-afd4-e99091765d1c.png">
</p>

- PANet style(경로 집계 네트워크)
  - 경로 연결을 통해 하위 레이어와 상위 피쳐간의 정보흐름을 개선해 주는 네크워크
- 왼쪽 2개 R : neck에 결합된 backbone의 다양한 깊이 피쳐
- 오른쪽 4개 : neck의 시작부분의 피쳐와 마지막 부분의 피쳐 연결
- Neck → C3(ConvBNSiLU, BottleNeck2), Upsample, Concat
  - 많은 C3블록으로 이루어져 있으며, 잔차 연결부가 없는 bottleneck 블록을 사용한다
- upsample(new layer block) : 이미지의 해상도 높이기


### Head : responsible for the predictions
추출된 Feature map을 바탕으로 하여 실제 물체 위치를 찾는 부분이다. Anchor Box를 처음에 설정하고 이를 이용하여 최종 Bounding Box를 생성한다. v3와 동일하게 3가지 scale(8 픽셀의 작은물체, 16 픽셀의 중간물체, 32 픽셀의 큰 물체)에서 바운딩 박스를 생성한다. 또, 이러한 스케일에서 각각 3개의 Anchor Box를 사용하니. 결론적으로는 총 9개의 앵커박스를 사용하게 된다. 


<p align="center">
  <img src="https://user-images.githubusercontent.com/51469989/216250070-0df7994b-df1e-4781-806c-666a7d43f3e8.png">
</p>


## Training Strategies
- 멀티 스케일 트레이닝(0.5~1.5x)
- AutoAnchor(커스텀 데이터 교육용)
- 워밍업 및 코사인 LR 스케줄러
- EMA(지수이동평균)
- 혼합 정밀도
- 진화하는 하이퍼 매개변수

## Compute losses
YOLOv5의 loss는 아래 3가지의 합으로 이루어져있다. 각 Scale(v5는 3개)의 각 Grid에 대해 모두 더해서 구하는 것이다. <sub>[참고 : VOLO v5, v6 Loss](https://leedakyeong.tistory.com/entry/Object-Detection-YOLO-v5-v6-Loss)</sub>
- Classes loss(BCE loss) : Class를 잘 찾기위한 Loss
- Objectness loss(BCE loss) : 해당 Grid 안에 물체가 있을지 없을지에 대한 Loss
- Location loss(CloU loss) : Center Point(x, y)와 Width, Height를 잘 찾기위한 Regression Loss

### Balance Losses
각 Scale 별 가중치

<p align="center">
  <img src="https://user-images.githubusercontent.com/51469989/216251545-02271432-d526-4115-a912-752d12089899.png">
</p>

### Eliminate Grid Sensitivity
YOLOv5에서 앵커 박스 영역의 경계 주변에서 예측 작업을 수행하기 위해, 전과는 약간 다르게 상자좌표를 정의. YOLOv5에서의 공식은 아래와 같다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/51469989/216251983-a5e90ea8-cbae-490f-ab0a-f2a9d7d99646.png">
</p>

