# 📃 재구매율 예측을 통한 구매물품 예측 및 솔루션 제안

<img src="https://user-images.githubusercontent.com/51469989/212594358-4afea532-b486-4175-9d00-db8cb54faf20.png" width="60%">

# 01. 개요
## 자료구성
- 분석대상(Analysis Target) : 2014년 ~ 2015년(2년간) L그룹 4개 계열사에서 구매한 고객 일부 발췌
- 제공범위(Scope of Offer) : L그룹 4개 계열사의 구매이력(계열사 정보 비공개), 고객 성향을 파악할 수 있는 데이터 제공

## 사용 모듈
- Google Colab <br>
- mljar <br>
- supervised <br>

## 주어진 사용 데이터 목록 및 컬럼내역
- 구매내역 정보 : 제휴사(ABCD)/영수증번호/대분류코드/중분류코드/소분류코드/고객번호/점포코드/구매일자/구매시간/구매금액
- 채널(온/오프라인) 이용 : 2015년 10월 ~ 12월. 총 3개월치
- 경쟁사 이용 : L사 4개 계열사 별 경쟁사 이용년월 정보
- 멤버십여부 : 멤버십에 가입한 고객 
- 데모(고객정보) : 20341개의 고객정보
- 상품분류 : 대분류, 중분류, 소분류로 나뉘어진 상품 분류 중 소분류 데이터 사용 / 4386개
<br>
→ 고객의 구매 패턴 파악을 위해 고객과 중심 데이터로 정리 및 가공<br>

## 데이터 가공 및 정리 목록
### 대분류 코드
원 데이터의 대분류 코드 가공 및 대분류명 라벨링. 분류는 유통상품지식뱅크¹의 상품 분류 기준을 따라 진행하였다.<br>
단, 명품의 경우에는 상품 하나의 가격이 크기 때문에 물품 분석에 어려움이 있을 수도 있다고 판단, 카테고리를 추가로 생성하여 분류해 주었으며 종합 선물세트, '행사' 등과 같이 상품의 상세 파악이 어렵거나 백화점 내 푸드코드와 패밀리 레스토랑과 같은 상품의 범주에 들어가지 않는다고 판단되는 것들은 구분을 위하여 기타 카테고리를 생성하여 분류해주었다.<br><br>

⋗ 대분류명 수정 및 데이터 반영 <br>
![image](https://user-images.githubusercontent.com/51469989/212466414-62781909-76c4-41ed-bf0a-e60dcd3af0dc.png)<br>

<sub> ¹ 유통상품 지식 뱅크 : 국내 시장에서 유통되는 상품의 정보들을 표준화된 데이터베이스로 관리하여, 이를 제공하는 '유통상품 종합정보서비스'</sub>
<br>

### 중분류 코드
같은 상품임에도 가방, 가방브랜드, 핸드백 등으로 분리가 되어있는 것을 발견. 물품군을 확실히 하기 위하여 유통상품지식뱅크의 상품 분류 기준을 따라 분류<br>

⋗ 중분류명 수정 및 데이터 반영 <br>
<img src="https://user-images.githubusercontent.com/51469989/212467218-1368459b-382e-41df-abd1-922eeebb75c7.png" width="35%"><br><br>

# 02. 주제 선정
## EDA(Exploratory Data Analysis, 탐색적 데이터 분석)
⋗ 고객 성별 매출 비교 <br>
![성별_매출_비교](https://user-images.githubusercontent.com/51469989/212464317-4d828dbe-df90-42a4-aec5-f5fa56830ce4.png) <br>
→ 남성에 비해 여성고객에게서 나온 매출액이 압도적으로 높은 모습 <br><br>
 
⋗ 연령별 매출 비교<br>
![KakaoTalk_20230113_170409050_01](https://user-images.githubusercontent.com/51469989/212464632-28b62a7e-5a0d-4a5f-97e7-e316a5612ce4.png)<br>
→ 40대 고객의 매출액이 높은 편이며, 연령대가 높을 수록 매출액도 큰 편 <br><br>

⋗ 성별_연령별 매출 비교<br>
![성별_연령별_매출비교](https://user-images.githubusercontent.com/51469989/212464359-0d54d532-ce46-4c21-90cb-f6b3dd2549cc.png)
![성별_연령별_매출비교_2](https://user-images.githubusercontent.com/51469989/212464353-efb5f78e-1a3e-48dc-8eab-3a6ec301e4ec.png) <br>
→ 남성고객의 경우에는 35세부터 39세의 고객군이 일순위이며, 45세 49세 고객군과 40세부터 44세의 고객군 순서로 매출액이 많은 모습<br>
→ 여성고객의 경우에는 45세부터 49세의 고객군이 가장 많았으며, 순서대로 40세부터 44세의의 고객군과 50세부터 54세의 고객군이 그 뒤를 이었다.<br><br>

⋗ 2014년 카테고리별 매출 기여도<br>
![2014년 매출기여도](https://user-images.githubusercontent.com/51469989/212477691-84ab0e5b-ea27-46da-a4ba-ea73cd2caf64.png)<br>
→ 2014년 카테고리별 매출 기여도는 의류 34.41%, 가공식품 48.87% 순으로 높았으며 의약품 및 의료기기 항목이 가장 낮은 기여도
<br><br>


⋗ 2015년 카테고리별 매출 기여도<br>
![2015년 매출기여도](https://user-images.githubusercontent.com/51469989/212477720-4aba2492-9f9c-45d9-b006-c41387d69217.png)<br>
→ 2015년의 카테고리별 매출 기여도 역시 의류 32.96%, 가공식품 46.74% 순으로 높았으며 의약품 및 의료기기의 항목이 가장 낮은 기여도<br>
→ 14년에는 매출 기여도 5순위였던 명품 항목이 15년에는 3순위로 변경 되었다는 점이 특징<br>
<br><br>

⋗ L사의 반기별 총 매출액 + 년도별 총 매출액 <br>
![image](https://user-images.githubusercontent.com/51469989/211510762-4a79a3df-63e7-4bbf-96d3-fe9efa34c559.png)
![image](https://user-images.githubusercontent.com/51469989/211510432-dcc72317-60af-4029-842a-79b30ad416ce.png)<br>
→ 계절에 따라 증감은 있지만 전체적으로는 증가하는 모습<br>
→ 그렇다면 신규 고객의 수가 많아서 매출이 증가했는가? <br><br>

⋗ 신규 고객의 수<br>
<img src="https://user-images.githubusercontent.com/51469989/211729119-cc1043e0-adea-4583-b851-aec161d4ffd6.png" width="60%"/>
<img src="https://user-images.githubusercontent.com/51469989/211729343-ee4d2912-c96a-4a41-b4f7-884476f2edab.png" width="20%"/><br>
→ 14년도 2분기의 유입 고객의 수는 유의미 할 수도 있는 수치인 141명이지만, 2년간의 전체 신규고객은 총 297명으로 전체 고객수에 비해서는 매우 적은 수<br>
→ 이는 기존 고객의 매출 기여도가 매우 높은 것으로 확인<br> <br>

⋗ 기존고객들을 분석하기 위해 영수증으로 고객별 매출 기여도를 확인<br>
![image](https://user-images.githubusercontent.com/51469989/211734406-22326994-b5f2-4d43-bf35-c629d442079a.png)<br>
→ 같은 상품을 반복 구매하는 횟수가 많은 고객 중 임의로 선택된 5명의 매출<br>
![image](https://user-images.githubusercontent.com/51469989/211734791-94dacbba-8638-44a5-8d5c-375f22b58072.png)<br>
→ 같은 상품을 반복 구매하는 횟수가 적은 고객 중 임의로 선택된 5명의 매출<br>
![image](https://user-images.githubusercontent.com/51469989/211738242-6a5da003-8a5c-4508-8efb-bff40e0d8e4d.png)<br>
→ 반복 구매 횟수가 적은 고객과 많은 고객(총 10명)을 함께 그린 그래프<br>
<br>
⋗ 그렇다면 같은 상품을 반복적으로 구매하는 것이 전체 매출에서 어느정도의 영향을 끼치는지 확인<br>
![KakaoTalk_20230113_175118578](https://user-images.githubusercontent.com/51469989/212464548-f608b899-5ee3-41a7-859b-cad8664254a9.png)
<br>
→ 2년간의 전체 매출에서 반복구매가 많은 고객들의 매출기여도는 53.76%로 반복구매가 적은 고객들에 비해서는 높은 편인 것을 확인할 수 있다.<br>
→ 여기에서 반복구매. 즉, 재구매율이 중요할수도 있을 것 같다는 과제 발견 <br>
<br>
⋗ 실제 마케팅 기법 중 '고객 유지 마케팅(Retention Marketing)' 이라는 것이 있다는 걸 확인<br>
<img src="https://user-images.githubusercontent.com/51469989/211989968-2b098d4c-4e38-4618-98bd-eee282627d9f.png" width="80%">
<br> 

  
## 주제 선정
재구매율 예측을 통한 구매물품 예측 및 솔루션 제안<br> 
<br> 
  
## 분석 과제
- 재구매율을 분석하여 재구매율이 낮은 고객과 높은 고객을 예측
- 군집화를 통해 다른 특성을 갖는 각 고객 군의 구매 패턴 파악 및 상품 추천을 제공

  
# 03. 재구매 예측을 위한 데이터 분석
## 정리한 변수속성
<table>
  <tr>
    <th>cust id</th>
    <td>고객번호</td>
  </tr>
  <tr>
    <th>total ordered goods</th>
    <td>총 구매 상품</td>
  </tr>
  <tr>
    <th>days between first and last order</th>
    <td>첫구매와 마지막 구매 사이의 간격</td>
  </tr>
  <tr>
    <th>days between orders</th>
    <td>구매일간의 간격</td>
  </tr>
  <tr>
    <th>number of orders</th>
    <td>구매 횟수</td>
  </tr>
  <tr>
    <th>kinds of ordered goods</th>
    <td>구매 상품 종류</td>
  </tr>
  <tr>
    <th>mean of goods in order</th>
    <td>구매 상품 갯수의 평균</td>
  </tr>
  <tr>
    <th>sex</th>
    <td>고객 성별</td>
  </tr>
  <tr>
    <th>age</th>
    <td>고객 나이</td>
  </tr>
  <tr>
    <th>address</th>
    <td>고객의 주소</td>
  </tr>
  <tr>
    <th>label</th>
    <td>재구매율</td>
  </tr>
</table>

  
### 재구매율이란
![image](https://user-images.githubusercontent.com/51469989/212595260-713f7ee7-69ab-42e1-8aa2-7f105940230a.png)<br>
→ 원래 재구매율의 정의 : 제품을 구매한 고객이 다시 해당 쇼핑몰을 찾아 구매하는 <br>
→ 이를 조금 더 상세하게 상품별로 분석해 보기 위하여 고객의 구매이력을 분석, 해당상품의 구매이력이 연속적으로 이어지는 지 아닌지로 영수증 마다 0과 1을 라벨링 한 뒤 이를 활용하여 고객별 물품으로 각각 구해진 재구매율을 모두 더한 후 전체 대비 재구매 라벨링 된 것의 비율을 재구매율로 삼았다.br>




### Train Dataset
- 2014년 ~ 2015년 3분기 데이터


### Test Dataset
- 2015년 4분기 데이터


# 04. 재구매율 예측을 위한 모델링
## Model
- 다양한 모델을 적용해보기  AutoML 중 mljar-supervised 패키지 사용<br>
mljar-supervised는 테이블 형식 데이터에 작동하는 자동화된 기계 학습 파이썬 패키지. 데이터를 전처리하고, 기계 학습 모델을 구성하며, 하이퍼 매개 변수 조정을 수행하여 최상의 모델을 찾는 일반적인 방법을 추상화하여 준비된 다양한 ML모델을 한번에 매우 쉽게, 효과적으로 수행해볼 수 있다.<br>
→ Iternated Tasks 자동화<br>
→ 알고리즘 선택 및 파라미터 최적화<br>
→ 튜닝 자동화<br>
→ 최적 모델 추천<br>

<br>

- mljar-supervised로 돌아간 모델의 correlation_heatmap
![correlation_heatmap](https://user-images.githubusercontent.com/51469989/211829844-f72533d3-f60e-43ad-864f-8ee34fb0524b.png)<br>

### Train-Val 검증 Dataset
AutoML로 학습 및 검증된 총 84개의 모델의 [Accuracy | Precision | recall | F1-score | auc] 성능을 비교하여 대체적으로 좋은 성능을 보였던 상위 5개 모델을 비교해본 모습은 아래 표와 같다. <br>

<img src="https://user-images.githubusercontent.com/51469989/212470966-43840f88-f55b-4cd2-824e-81dd2578a9c0.png" width="60%"> <br><br>

이 중에서 F1-Score를 중점으로 하여 깊이 6의 CatBoost로 최적화 후 학습을 재진행하기로 하였다

#### Accuracy가 아닌 F1-Score를 중점으로 모델을 선택한 이유
먼저 우리가 사용한 재구매율을 구한 방법은 아래와 같다.<br><br>

고객의 구매내역을 분석하여 각 고객이 특정 상품을 연속하여 구매했는지의 여부를 판단하여 구매내역(영수증의) 갯수만큼 (구매시 1, 비 구매시 0이라는) 라벨을 달아준 후, 전체갯수에서 1의 갯수를 나눠준 값을 재구매율로 삼고 이를 반영하였다.<br>

여기에서 Accuracy가 아닌 F1-Score를 중점으로 본 이유가 나오는데. 고객이 연속해서 물건을 구매하는 경우(1)가 전체 구매 대비 매우 적은 수였기 때문에 데이터의 불균형이 일어났다고 판단하였다. 이때 만약 모델의 성능을 Accuracy로 판단한다면 모델의 성능이 실제로 좋지 못하더라도 매우 좋게 나올 수도 있었기에,<br><br>
(예를 들어 100개의 dataset에서 90개의 데이터 라벨이 0, 10개의 데이터 라벨이 1이라고 한다면. 모델이 학습결과로 0만을 내뱉어도 정확도는 90%가 나오게 된다)<br>

단순히 모델이 정답을 맞췄는가/틀렸는가를 평가지표로 삼는 Accuracy보다는 정밀도¹와 재현율²의 수치가 적절하게 조합된 평가지표인 F1-Score를 중점으로 삼는 것이 좋겠다는 결과를 도출, 따라서 F1-Score로 평가한 것이다.<br>

<sub>정밀도¹(Precision) : 모델이 Positive라고 예측한 것들 중 실제 Positive였던 확률 -> TP / (TP + FP)<br>
만약 Negative를 Positive로 오판(ex. 음성 데이터를 양성으로 잘못 판단)한다면 정밀도가 떨어지는 것이라 판단할 수 있다. </sub>

<sub>재현율²(Recall) : 실제 Positive 중 모델이 Positive라고 예측한 확률 -> TP / (TP + FN)<br>
만약 Positive를 Negative로 예측(ex.양성 데이터를 음성 데이터로 잘못 판단)한다면 재현율이 떨어지는 것이라 판단할 수 있다.</sub><br>


#### AutoML로 선정한 top5모델의 confusion matrix와 auc 곡선 이미지 비교(+ 모델특징
1. CatBoost (depth : 6) <br>
 - 범주형 변수의 예측모델에 최적화된 모델(범주형 데이터를 처리하는 새로운 방법 제시)
 - 다른 GBM에 비해 과적합 적다 / 범주형 변수에 대해 모델의 정확도&속도 높다 / encoding 작업 없이 모델의 input 가능<br>
 
![image](https://user-images.githubusercontent.com/51469989/212472957-78c11360-ca2f-4824-b951-330a82a3da6a.png)
![image](https://user-images.githubusercontent.com/51469989/212472967-44f68a1c-dd12-45d5-8de9-24e15bda8538.png)<br><br>

2. CatBoost (depth : 8) <br>
<img src="https://user-images.githubusercontent.com/51469989/212596376-dd3f0643-0727-4987-85b6-012c1d4dc247.png" width="60%">

![image](https://user-images.githubusercontent.com/51469989/212473012-e62596fb-1b29-4ecd-9af9-bfed1de9709b.png)<br><br>

3. LightGBM <br>
- XGBoost보다 학습시간&메모리 사용량 적다 / 기능성의 다양성도 더 많다
 - 카테고리형 피처의 자동 변환이 가능하고 최적 분할이 가능<br>
![image](https://user-images.githubusercontent.com/51469989/212473017-7b2cf750-89fc-48b1-bd84-742cb05e1aff.png)
![image](https://user-images.githubusercontent.com/51469989/212473022-faf0cf5d-54ed-4852-a368-3184e08f25b4.png)<br><br>

4. Xgboost <br>
- 트리 기반의 앙상블 학습모델<br>
- 뛰어난 예측 성능 / GBM 대비 빠른 수행시간 / 과적합 규제 기능 / 결손값 자체 처리<br>
![image](https://user-images.githubusercontent.com/51469989/212473029-3578fa37-727e-49c6-9fbd-fbff1d018f03.png)
![image](https://user-images.githubusercontent.com/51469989/212473032-c92f7740-9492-46b2-84a1-ec9b6fe9aaa5.png)<br><br>


5. Random Forest <br>
- 과대 적합(overfitting) 을 방지하기 위해, 최적의 기준 변수를 랜덤하게 선택<br>
 - 일반화 및 성능 우수 / 파라미터 조정 용이 / scale 변환 불필요 / 과적합 잘 안된다
![image](https://user-images.githubusercontent.com/51469989/212473034-e211264b-2ae3-4159-a373-7aa9b915b77f.png)
![image](https://user-images.githubusercontent.com/51469989/212473038-a2f8affb-fd65-42e8-be7f-f75625adbe80.png)<br><br>

  
## 최적화 후 Train-Val, Test 결과
<img src="https://user-images.githubusercontent.com/51469989/211951684-a636c369-8843-4d9d-8599-2f1e999b470d.png" width="40%"> <br>
f1_score 약 82% , accuracy_score는 약 85% 의 결과가 도출되었다.

### Feature Importance
<img src="https://user-images.githubusercontent.com/51469989/211688348-da43b444-4a1a-4c47-8539-77a0ffd5b008.png" width="60%"> <br>


## 재구매율 예측 결과
→ 추후 예측 재구매율이 낮은 고객 11,722명 <br>
→ 추후 예측 재구매율이 높은 고객 7,89명


# 05. INSIGHT
## Clustering
### 사용 변수 및 속성
<table>
  <tr>
    <th>구매상품수</th>
    <td>고객이 구매한 상품의 갯수</td>
  </tr>
  <tr>
    <th>첫-마지막</th>
    <td>첫구매와 마지막 구매 사이의 간격</td>
  </tr>
  <tr>
    <th>구매건수</th>
    <td>고객이 구매한 횟수(영수증 갯수)</td>
  </tr>
  <tr>
    <th>구매항목수</th>
    <td>고객이 구매한 상품의 항목 수</td>
  </tr>
  <tr>
    <th>영수증평균개수</th>
    <td>고객이 구매한 횟수의 평균</td>
  </tr>
  <tr>
    <th>구매금액평균</th>
    <td>고객이 구매한 금액의 평균</td>
  </tr>
  <tr>
    <th>구매간격</th>
    <td>고객의 구매 간격 평균</td>
  </tr>
  <tr>
    <th>구매_평균항목수</th>
    <td>고객이 한번 구매시 평균적으로 몇가지의 상품을 구매했는가</td>
  </tr>
  <tr>
    <th>총구매금액</th>
    <td>고객의 총 구매금액</td>
  </tr>
  <tr>
    <th>총영수증개수</th>
    <td>고객의 총 영수증 갯수</td>
  </tr>
  <tr>
    <th>최다구매물품</th>
    <td>고객이 가장 많이 구매한 상품</td>
  </tr>
  <tr>
    <th>재구매율</th>
    <td>영수증 분석으로 얻어진 고객의 평균 재구매율</td>
  </tr>
</table>
<br>
→ 위와 같은 중요변수 12개를 추출하여 군집화 진행<br>

### Clustering을 위하여 사용한 모델
![image](https://user-images.githubusercontent.com/51469989/212595835-720ace35-b5b5-4302-be51-29f1f782bcf1.png)
→ 이 중 적은 군집 수로도 고른 분포를 가지며 평균 실루엣 점수 역시 0.75로 높은 모습을 보인 K-Means로 군집화를 이어 진행하기로 함


### Elbow-Method
최적의 군집 수를 구하기 위해 사용<br>
![엘보](https://user-images.githubusercontent.com/51469989/211835206-b1906cc7-35bc-455a-b3f9-cad3fe7d4ece.png)<br>
Cluster 간의 거리 합을 나타내는 inertia가 급격히 떨어지는 지점의 K값을 군집의 개수로 사용해볼 수 있는데, 위 그래프에 의하면 K 값은 3 또는 4가 적당해보인다.
  

### Silhouette Coefficient
클러스터링 평가 지표 중 하나<br>
군집끼리 서로 잘 구분되는지, 군집 안에 데이터들이 잘 모여있는지를 시각적으로 확인하며 클러스터링을 평가해보았다.<br>
<img src="https://user-images.githubusercontent.com/51469989/211835956-fb547723-8fe9-404a-b4de-74f064b00652.png" width="50%">
<img src="https://user-images.githubusercontent.com/51469989/211837158-59863770-5c09-4a49-b2f1-9e34c30010d1.png" width="50%"><br>
K=3으로로 군집화한 것이 K=4로 군집화 한 것보다 데이터 포인트들의 실루엣 계수 값이 완만하며 고른것을 확인할 수 있었다. <br>
<br>
→ K=3으로 군집화 하기로 결정<br>

## Clustering 특성
- 각 군집별 전체 비율
<img src="https://user-images.githubusercontent.com/51469989/211960409-8e8f8f24-a063-4f92-be3b-47831f92fff1.png" width="40%"> <br>
A 군집 : 6301명 (0 : 5594 명 / 1 : 707 명)<br>
B 군집 : 6795명 (0 : 4415 명 / 1 : 2308 명)<br>
C 군집 : 6287명 (0 : 1713 명 / 1 : 4574 명)<br>
<sub> 1 : 재구매 가능성이 높게 예측된 고객</sub> <br>
<sub> 0 : 재구매 가능성이 낮게 예측된 고객</sub><br>
<br>

<img src="https://user-images.githubusercontent.com/51469989/212596164-fd1e54e8-5263-4c36-8b2f-51d61ff46089.png" width="80%">

- 각 군집별 AOV(AVERAGE ORDER VALUE; 주문 건수 당 평균 결제금액) 평균

<img src="https://user-images.githubusercontent.com/51469989/212026245-a0ccbb71-45c4-4148-82f6-09e46d7df6de.png" width="80%"><br>
A 군집 : AOV가 낮음 = 가격이 저렴한 것들을 자주 사는 부류<br>
B 군집 : AOV 보통 = 보통<br>
C 군집 : AOV가 높음 = 가격이 높아도 구매가능한 부류 <br>

<br>

## 각 군집별 특성 분석
### A 군집
- 특성<br>
구매간격이 짧고 구매항목 수는 많으나, 전체적인 재구매율은 낮은편<br>
가격이 저렴한 것들을 자주 구매하는 고객군<br>
카테고리 중 식품과 일상용품의 구매율이 높은편<br>

  
### B 군집
- 특성<br>
구매간격과 재구매율은 보통이나, 구매항목 수가 많은편<br>
가격 민감도가 낮은편의 고객군<br>
카테고리 중 식품과 의류/패션의 구매율이 높은편<br>

  
### C 군집
- 특성<br>
구매간격이 비교적 길고 구매항목수는 적으나, 재구매율은 높은편<br>
높은 가격에 크게 영향을 받지 않는 고객군<br>
카테고리중 의류/패션/명품의 구매율이 높은편 <br>
  



# 06. 군집별 마케팅 제안
## A, B군집에 사용될 추천 시스템
각 군집의 특성을 파악하여 군집 내 재구매율이 높은 고객군에서 구매 예측된 물품을 재구매율이 낮은 고객군에게 가장 효율적인 추천시스템으로 적용<br>
- A군집 : 서프라이즈 패키지의 SVD를 활용<br>
- B군집 : KNN(최근접 이웃기반 협업필터링) 활용<br>
<br>
→ C군집에 비하여 재구매율이 낮은 편이기 때문에 현재 구매 물건으로 추천을 하기보다 조금이라도 재구매율을 높일 수 있게끔, 재구매 가능성이 높게 예측된 고객군의 구매물품을 분석(예측)하여 추천<br>
→ 즉, 사용자를 기반으로 물품 추천을 할 수 있도록 한다.<br>
<br>

### 재구매 가능성이 높은 물품을 예측하는 모델
A와 B군집에서 재구매 가능성이 높게 예측된 고객들을 대상으로 재구매 가능성이 높은 물품을 예측하는 모델 제작<br>

#### 사용 변수 속성
<table>
  <tr>
    <th>고객번호</th>
    <td>고객 id</td>
  </tr>
  <tr>
    <th>영수증번호</th>
    <td>구매한 영수증 번호</td>
  </tr>
  <tr>
    <th>구매상품수</th>
    <td>해당 상품 구매 횟수</td>
  </tr>
  <tr>
    <th>첫-마지막</th>
    <td>첫 구매과 마지막 구매사이의 기간</td>
  </tr>
  <tr>
    <th>구매간격</th>
    <td>연속된 두 구매사이의 기간의 평균</td>
  </tr>
  <tr>
    <th>구매건수</th>
    <td>구매한 전체 상품수</td>
  </tr>
  <tr>
    <th>구매항목수</th>
    <td>구매한 상품 항목 수</td>
  </tr>
  <tr>
    <th>구매_평균항목수</th>
    <td>구매 당 평균 항목 수</td>
  </tr>
  <tr>
    <th>reorder_rate</th>
    <td>상품이 처음 구매 되어지고 두번째에 다시 재구매 되어진 비율</td>
  </tr>
  <tr>
    <th>소분류코드</th>
    <td>물품의 소분류 코드</td>
  </tr>
  <tr>
    <th>상품당_총구매수</th>
    <td>각 상품당 총 구매 수 </td>
  </tr>
  <tr>
    <th>처음_두번째주문비율</th>
    <td>처음 구매 되어지고 두번째에 다시 재구매 되어진 비율</td>
  </tr>
  <tr>
    <th>각 상품 먼저 구매고객 수</th>
    <td>가장 먼저 카트에 넣은 고객 수</td>
  </tr>
  <tr>
    <th>전체판매량/첫번째구매</th>
    <td>전체 판매량 대비 첫번째로 구매 되어진 비율</td>
  </tr>
  <tr>
    <th>평균구매량</th>
    <td>각 상품을 구입한 고객의 평균 구매 횟수</td>
  </tr>
  <tr>
    <th>cnt</th>
    <td>총 재구매 수</td>
  </tr>
  <tr>
    <th>goods_reorder_rate</th>
    <td>구매한 전체 상품 중 재구매한 상품의 비율</td>
  </tr>
  <tr>
    <th>특정상품구매횟수</th>
    <td>특정 상품 구매 횟수</td>
  </tr>
  <tr>
    <th>특정상품포함비율</th>
    <td>전체 구매횟수 중 특정 상품을 포함하는 비율</td>
  </tr>
  <tr>
    <th>처음구매횟수</th>
    <td>각 고객이 특정 상품을 첫번째로 구매한 주문회차</td>
  </tr>
  <tr>
    <th>마지막구매횟수</th>
    <td>각 고객이 특정 상품을 마지막으로 구매한 주문회차</td>
  </tr>
  <tr>
    <th>전체구매횟수/특정상품포함</th>
    <td>특정 상품을 마지막으로 구매하고 난 후의 구매횟수</td>
  </tr>
  <tr>
    <th>특정상품구매후_특정상품구매비율</th>
    <td>특정 상품을 사고 난 후 특정 상품을 포함 하는 구매의 비율</td>
  </tr>
</table>

#### Dataset
Train-val Dataset : 2014년 ~ 2015년 3분기 데이터<br>
Test Dataset : 2015년 4분기 데이터

#### Model
- Decision Tree <br>
- LightGBM <br>

→ 모델간의 correlation_heatmap <br>
<img src="https://user-images.githubusercontent.com/51469989/211970391-0bdee485-f745-4f5d-b10c-605b1435c9eb.png" width="50%"><br><br>

→ 모델들의 f1-Score 결과<br>
![image](https://user-images.githubusercontent.com/51469989/212474770-eb20784e-112c-45cd-ab1e-55e95f42f2b6.png)<br>
→ 가장 f1_score가 높게나온 LightGBM으로 최적화(learning_rate = 0.5, num_leaves = 15) 후 학습을 재진행하기로 결정하였다.<br><br>

- Test-Accuracy score <br>
<table>
  <tr>
    <th>auc</th>
    <th>accuracy</th>
    <th>f1</th>
    <th>precision</th>
  </tr>
  <tr>
    <td>약 0.651</td>
    <td>약 0.913</td>
    <td>약 0.351</td>
    <td>약 0.354</td>
  </tr>
</table>
<br>
한계점 : 구매내역(행)의 수가 너무 많아 사용하는 Colab환경 내 RAM과 런타임으로는 복잡한 모델에서는 학습진행 및 결과 도출에 있어 문제가 발생하였다. 따라서 DecisionTree, LightGBM 추가적으로 Logistic까지, 비교적 간단한 모델로 학습을 진행할 수 밖에 없었다. 그러한 상황에서 맞춰야할 정답 레이블인 소분류의 상품 수가 3519개로 많아 Score가 낮게 나온 것 같아 아쉬움이 남는다.
<br><br>

- Feature Importances <br>
<img src="https://user-images.githubusercontent.com/51469989/211987596-30714c0b-cb81-4639-9929-386aad20ace4.png" width="50%"><br>

### A군집 마케팅 제안
<img src="https://user-images.githubusercontent.com/51469989/212078018-d6a91b44-aabf-401a-a21c-a7adb85d06d8.png" width="60%">
<br>
- 서프라이즈 패키지의 SVD를 활용 예측된 물품 중 식품과 일상용품 카테고리의 물품을 선별해 추천을 진행한다.<br>
- 식품의 경우 가격 변동이 크지 않고 수요의 탄력성이 높지 않은 상품군이며, 저렴한 가격의 상품들을 위주로 소비하는 패턴이 발견되었기 때문에 해당 상품들의 할인 행사, 이벤트 등으로 수요를 높일 수 있을 것이라 예상된다.<br>

### B군집 마케팅 제안
![image](https://user-images.githubusercontent.com/51469989/212532624-ff555194-779e-4870-be2b-74e84b8b89fa.png)
<br>
- B군집의 경우 재구매 가능성이 높게 예측된 고객군(4415명)과 재구매 가능성이 낮게 예측된 고객군(1308명)이 고르게 분포하고 있는 모습 확인
- 따라서 KNN(최근접 이웃기반 협업필터링)을 활용해 구매 가능성이 높게 예측된 물품 중 식품과 의류/패션 카테고리의 물품을 선별해 추천을 진행시 수요를 높일 수 있을 것이라 예상된다.

<br>

## C군집 <br>
→ A, B군집에 비하여 재구매율이 높은 편이므로 구매물품의 연관규칙을 분석하여 추쳔<br>
→ 아이템 기반 추천 시스템 <br>
→ 기존 장바구니 분석(연관규칙분석; apriori algorithm;) 활용 <br>

### 장바구니 분석
연관 분석(Association Analysis) 이라고도 불리는 장바구니 분석은 대량의 정보로부터 개별 데이터 사이의 연관규칙을 찾는 것인데, 예를 들어 마켓의 구매내역 중 특정 물건의 판매 발생 빈도를 기반으로 'A물건을 구매하는 사람들은 B물건을 함께 구매하는 경향이 있다' 라는 규칙을 찾을 수 있다.

연관규칙분석의 대표적인 알고리즘으로는 Apriori, FP-growth, DHP 등이 있는데 그 중 Apriori 알고리즘이 비교적 구현이 간단하고 높은 성능을 보여준다는 의견이 많아 프로젝트에 Apriori을 사용해보기로 했다.

### Apriori 알고리즘
추천시스템의 1세대라고 할 수 있는 Apriori 알고리즘은 빈발항목집합₁을 추출하는 것이 원리이다. 이해하기 쉬운 간단한 원리를 가지고 있으며 상품간의 많은 연관규칙을 발견할 수 있다는 장점이 있지만, 상품 수가 많을 수록 그 계산량이 기하급수적으로 늘어난다는 단점 역시 가지고 있다.<br><br>

<sub>₁ 빈발항목집합 : 최조지지도 이상을 가지는 항목집합. 모든 항목집합을 대상으로 했을 시 계산량이 복잡해질 수 있어, 최소지지도를 정한 후 그 이상의 값만 찾아 연관규칙을 생성한다. </sub> <br>

### C군집 마케팅 제안
![image](https://user-images.githubusercontent.com/51469989/212216672-f06087a6-2bd4-4337-9223-8682776d8cec.png)
<br>
- 연관규칙분석 알고리즘인 Apriori를 활용하여 분석된 물품을 활용하여 고객별 맞춤 추천 서비스 제공
- 가격 민감도가 낮고 명품의 구매율도 높은 편이기 때문에 사치품에 대한 마케팅을 진행


## 기대효과
상관계수를 통해 구해본 결과, 해당 마케팅제안으로 재구매율이 10% 정도 상승함을 가정했을때 최종 예상 매출은 2년간 전체매출 대비 2-3% 증가할 것으로 계산되었다. 이는 만약 매출 증가액이 2.5%라고 가정한다면 약 169,254,789,000원(천육백구십이억 오천사백칠십팔만 구천원)의 추가 매출을 기대해 볼 수 있다는 것이다.
