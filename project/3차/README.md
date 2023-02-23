# 손 엑스레이 분석을 통한 연령 및 키 성장 예측

### 참고 논문
* [YOLO (You Only Look Once: Unified, Real-Time Object Detection)](https://github.com/semi0612/CNN_paper/blob/master/Reading/YOLO.md)
* [YOLOv5 Summary](https://github.com/semi0612/study/blob/main/project/3%EC%B0%A8/YOLOv5%20summary.md)
* [TJNet]()

![image](https://user-images.githubusercontent.com/51469989/220557718-df7d92e2-1b27-40ee-b794-9dbe6c07e3e4.png)

## 제작환경 및 모듈
```python
python + colab
OpenCV
PyTorch
TensorFlow
```

## 프로젝트 설명
왼쪽 손목과 손가락 관절이 보이는 X-ray사진으로 YoLov5와 Tjnet 딥러닝을 통해 골연령을 예측<br>
예측된 골연령으로 질병관리청에서 제공된 '소아 청소년 성장도표'를 활용하여 18세 기준 예상 신장을 도출한다.<br><br>

프로그램 사용을 용이하게 하기 위해 PyQT5를 활용하여 GUI를 구성해보려 함

### 골연령이란
X-ray 검사 상의 보이는 나이를 뜻하며 골 성숙도를 나타내는 개념으로<br>
이러한 골연령을 통해 성 조숙증과 같은 유아의 성장관련 질환을 판별가능하고, 측정된 현재의 골연령을 통해 18세때의 성장 키를 예측해보는것이 가능하다.<br>


## 프로젝트 절차
### 이미지 전처리
기존 엑스레이 이미지를 Opencv 모듈을 활용하여 배경 제거 후 손영역만 추출하고, 모델의 정확도를 높이기 위해 이미지의 각도를 조정했다. 그 후 모폴로지 연산을 통해 노이즈를 제거하고 값이 큰 구간을 강조. 마스크 생성 및 뼈를 도출한다.

### 이미지 라벨링
골 연령 측정 방법 중 TW3 기법을 활용하여 이미지를 라벨링 한다. Roboflow를 이용해 필요한 관절 부분을 Annotation 하고 이를 YOLOv5를 활용하여 관절 객체 탐지를 진행하였다. activation 등을 변경해가며 24개의 모델을 비교하며 mAP를 측정해본 결과, 평균 0.995의 성능을 보인 모델로 최종 선택하여 라벨링 처리를 완료하였다.

### 골 연령 예측
![image](https://user-images.githubusercontent.com/51469989/220562764-ee4e1aa9-0e77-41e8-953a-70e50fa9fc8d.png)<br>
![image](https://user-images.githubusercontent.com/51469989/220562823-37c1bbda-8b9f-420b-9983-302f8f680f9f.png)<br>
<br>
도출된 손가락 관절과 손목 데이터를 원본 이미지와 함께 TJNet 모델에 대입하여 학습을 진행하였다. 골연령의 경우 성별에 따라 골 성장 속도가 다르기 때문에 Gender데이터를 추가하여 성별에 따른 차이 또한 학습하도록 Train 을 진행하였다. 전체 데이터가 성별과 연령을 기준으로 크기가 다르기 때문에 Train-Test Set은 train_test_split를 활용하여 0.8:0.2의 비율로 만들어주었다.
<br><br>
모델 학습 결과 [MSE 0.269 / MAE 0.403] 의 결과를 보인 모델로 최종 선택하여 골 연령 예측을 진행하였습니다.

### 신장 예측
예측된 골연령을 활용하여 질병관리청에서 제공된 '소아 청소년 성장도표'를 활용하여 18세 기준 예상 신장을 도출하였다. <br><br>
예측 신장 계산 공식은 수정된 L,M,S 공식을 활용하였고 각각의 값은 L : Box-cox-power, M : Median , S : Coefficient of Variation 입니다. L, M, S의 값은 성장도표에 포함되어 있으며, 해당 연령의 신장과 그에 따른 분위수가 같이 포함되어 있습니다. L, M, S값으로 평균정규분포 (Z)를 구하고, 18세 기준 L, M, S 값과 평균정규분포(Z)로 검사자의 18세 예측 신장값을 도출했습니다. 도출된 예측 신장값을 검사자가 편하게 볼 수 있도록 질병관리청에서 제공한 '소아 청소년 성장그래프'와 동일하게 그래프를 그리고, 그 위에 현재 나이와 신장의 분위수와 예측 신장값을 표시해본 그래프는 아래와 같다.<br>
![image](https://user-images.githubusercontent.com/51469989/220564580-3fe40f7d-b8dc-4915-b2bb-8338c31df5a2.png)<br>

### GUI
골연령과 신장 예측 정보를 쉽게 사용해볼 수 있게끔 PyQT를 활용하여 GUI를 구성
