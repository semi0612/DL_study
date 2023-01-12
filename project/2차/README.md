# 📃 장바구니 분석을 통한 재구매 물품 예측
.
<img src="https://user-images.githubusercontent.com/51469989/210502490-5881ad58-4d8c-4fea-bfce-f6d04b565e89.png" width="60%">

# 01. 개요
## 자료구성
- 분석대상(Analysis Target) : 2014년 ~ 2015년(2년간) L그룹 4개 계열사에서 구매한 고객 일부 발췌
- 제공범위(Scope of Offer) : L그룹 4개 계열사의 구매이력(계열사 정보 비공개), 고객 성향을 파악할 수 있는 데이터 제공

## 사용 모듈
- Google Colab 

## 주어진 사용 데이터 목록 및 컬럼내역
- 구매내역 정보 : 제휴사(ABCD)/영수증번호/대분류코드/중분류코드/소분류코드/고객번호/점포코드/구매일자/구매시간/구매금액
- 채널(온/오프라인) 이용 : 2015년 10월 ~ 12월. 총 3개월치
- 경쟁사 이용 : L사 4개 계열사 별 경쟁사 이용년월 정보
- 멤버십여부 : 멤버십에 가입한 고객 
- 데모(고객정보) : 20341개
- 상품분류 : 4386개<br>
→ 재구매율을 계산하기 위해 고객x상품 위주의 데이터로 가공 및 정리<br>
→ 대분류, 중분류, 소분류로 나뉘어진 상품 분류 중 소분류 데이터 사용<br>

# 02. 주제 선정
## EDA
⋗ L사의 반기별 총 매출액 + 년도별 총 매출액 <br>
![image](https://user-images.githubusercontent.com/51469989/211510762-4a79a3df-63e7-4bbf-96d3-fe9efa34c559.png)
![image](https://user-images.githubusercontent.com/51469989/211510432-dcc72317-60af-4029-842a-79b30ad416ce.png)<br>
→ 계절에 따라 증감은 있지만 전체적으로는 증가하는 모습<br>
→ 그렇다면 신규(혹은 복귀) 고객의 수가 많아서 매출이 증가했는가? <br>
<br>
⋗ 신규(혹은 복귀) 고객의 수<br>
<img src="https://user-images.githubusercontent.com/51469989/211729119-cc1043e0-adea-4583-b851-aec161d4ffd6.png" width="60%"/>
<img src="https://user-images.githubusercontent.com/51469989/211729343-ee4d2912-c96a-4a41-b4f7-884476f2edab.png" width="20%"/><br>
→ 14년도 2분기의 유입 고객의 수가 제일 많은 편이지만 그마저도 141명, 총 297명으로 전체 고객수에 비해서는 매우 적은 수<br>
→ 이는 기존 고객의 매출 기여도가 매우 높은 것으로 확인<br>
<br>
⋗ 기존고객들을 분석하기 위해 영수증으로 고객별 매출 기여도를 확인<br>
![image](https://user-images.githubusercontent.com/51469989/211734406-22326994-b5f2-4d43-bf35-c629d442079a.png)<br>
→ 같은 상품을 반복 구매하는 횟수가 많은 고객 중 임의로 선택된 5명의 매출<br>
![image](https://user-images.githubusercontent.com/51469989/211734791-94dacbba-8638-44a5-8d5c-375f22b58072.png)<br>
→ 같은 상품을 반복 구매하는 횟수가 적은 고객 중 임의로 선택된 5명의 매출<br>
![image](https://user-images.githubusercontent.com/51469989/211738242-6a5da003-8a5c-4508-8efb-bff40e0d8e4d.png)<br>
→ 반복 구매 횟수가 적은 고객과 많은 고객(총 10명)을 함께 그린 그래프<br>
<br>
⋗ 그렇다면 같은 상품을 반복적으로 구매하는 것이 전체 매출에서 어느정도의 영향을 끼치는지 확인<br>
![image](https://user-images.githubusercontent.com/51469989/211816666-59a817a2-4c0f-4002-8070-518c549b1fa9.png)<br>
→ 년도별 전체 매출 속 반복구매가 많은 고객들의 매출기여도는 각각 약 67.2%, 약 67.8%로 반복구매가 적은 고객들에 비하여 매우 높은 편인 것을 확인할 수 있다.<br>
→ 여기에서 반복구매. 즉, 재구매율이 중요한 것 같다는 과제 발견 <br>
<br>
⋗ 실제 마케팅 기법 중 '고객 유지 마케팅(Retention Marketing)' 이라는 것이 있다는 걸 확인<br>
<img src="https://user-images.githubusercontent.com/51469989/211989968-2b098d4c-4e38-4618-98bd-eee282627d9f.png" width="80%">
<br> 

  
## 주제 선정
재구매율 예측 모델 생성 후 예측된 재구매율을 활용하여 구매 물품 예측 모델 생성 및 마케팅 제안<br> 
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
고객의 구매이력(영수증)을 분석하여 물품별로 구매 이력이 이어지는가 아닌가로 라벨링<br>
이를 활용해 고객별 물품별로 구해진 재구매율을 모두 더해 전체 대비 재구매한 것의 비율을 재구매율로 삼는다.<br>


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

<br>
- 학습-검증 Dataset f1-score Cnfusion Marix와 Roc Curve비교(상위 5개)<br>
1. CatBoost : 0.744486 <br>
<img src="https://user-images.githubusercontent.com/51469989/211957795-0c518cc8-828a-4d46-b9d0-3115e38b3f9f.png" width="50%">
<img src="https://user-images.githubusercontent.com/51469989/211957945-5c04127b-1f82-4b3c-947e-17c6913bfd95.png" width="50%"><br>
2. CatBoost : 0.740299 <br>
<img src="https://user-images.githubusercontent.com/51469989/211958020-cb8f3d53-451d-466b-ae7a-b4a771e8d320.png" width="50%">
<img src="https://user-images.githubusercontent.com/51469989/211958094-b7240dc3-b694-44ae-8bcc-a83cbfcd2afa.png" width="50%"><br>

3. LightGBM : 0.73771 <br>
<img src="https://user-images.githubusercontent.com/51469989/211958581-6cf00f94-96e1-4459-899b-9e2470c94da7.png" width="50%">
<img src="https://user-images.githubusercontent.com/51469989/211958488-2e704ad6-a2b1-4a35-8000-7a998cdb537d.png" width="50%"><br>

4. Xgboost : 0.729744 <br>
<img src="https://user-images.githubusercontent.com/51469989/211958744-358481b7-ec13-4890-a996-7e3843d807ec.png" width="50%">
<img src="https://user-images.githubusercontent.com/51469989/211958686-480bf1ed-8e56-4d7a-893a-92c464ac65d2.png" width="50%"><br>

5. Random Forest : 0.721279 <br>
<img src="https://user-images.githubusercontent.com/51469989/211958926-0229d4a3-17cc-4fc4-abcd-d02dba0f38a2.png" width="50%">
<img src="https://user-images.githubusercontent.com/51469989/211958871-6546ae00-a5c8-4798-8581-d8124d5bd42e.png" width="50%">

<br>

→ 가장 성능이 좋았던 CatBoost로 테스트 진행결정
  
## Test-Dataset 결과
<img src="https://user-images.githubusercontent.com/51469989/211951684-a636c369-8843-4d9d-8599-2f1e999b470d.png" width="60%"> <br>
f1_score : 약 82% <br>
accuracy_score : 약 85% <br>

### Feature Importance
<img src="https://user-images.githubusercontent.com/51469989/211688348-da43b444-4a1a-4c47-8539-77a0ffd5b008.png" width="40%"> <br>

## 재구매율 예측 결과
<img src="https://user-images.githubusercontent.com/51469989/211950959-871dd810-58e1-4432-a792-75da49b2f9e2.png" width="60%"><br>
→ 추후 재구매율이 낮은 고객 9691명 <br>
  추후 재구매율이 높은 고객 9692명


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
    <td고객의 총 구매금액</td>
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

### Elbow-Method
최적의 군집 수를 구하기 위해 사용<br>
![엘보](https://user-images.githubusercontent.com/51469989/211835206-b1906cc7-35bc-455a-b3f9-cad3fe7d4ece.png)<br>
Cluster 간의 거리 합을 나타내는 inertia가 급격히 떨어지는 지점의 K값을 군집의 개수로 사용해볼 수 있는데, 위 그래프에 의하면 K 값은 3 또는 4가 적당해보인다.
  

### Silhouette Coefficient
클러스터링 평가 지표 중 하나<br>
군집끼리 서로 잘 구분되는지, 군집 안에 데이터들이 잘 모여있는지를 시각적으로 확인하며 클러스터링을 평가해보았다.<br>
<img src="https://user-images.githubusercontent.com/51469989/211835956-fb547723-8fe9-404a-b4de-74f064b00652.png" width="50%">
<img src="https://user-images.githubusercontent.com/51469989/211837158-59863770-5c09-4a49-b2f1-9e34c30010d1.png" width="50%"><br>
K=3으로로 군집화한 것이 K=4로 군집화 한 것보다  실루엣 계수의 전체 값은 조금 높지만(붉은 점선 확인) 데이터 포인트들의 실루엣 계수 값이 완만하며 고른것을 확인할 수 있었다. <br>
<br>
→ K=3으로 군집화 하기로 결정<br>

## Clustering 특성
<img src="https://user-images.githubusercontent.com/51469989/211960409-8e8f8f24-a063-4f92-be3b-47831f92fff1.png" width="50%"> <br>
A 군집 : 6301명 (0 : 5594 명 / 1 : 707 명)<br>
B 군집 : 6795명 (0 : 4415 명 / 1 : 2308 명)<br>
C 군집 : 6287명 (0 : 1713 명 / 1 : 4574 명)<br>
 <br>
<sub> 1 : 재구매 가능성이 높게 예측된 고객</sub> <br>
<sub> 0 : 재구매 가능성이 낮게 예측된 고객</sub>


# 06. 마케팅 제안
## 추천 시스템 기반
각 군집 별 특성을 파악하여 군집 내 재구매율이 높은 고객군에서 구매 예측된 물품을 재구매율이 낮은 고객군에게 가장 효율적인 추천시스템으로 적용해보기로 했다. <br>
 
### 재구매 가능성이 높은 물품을 예측하는 모델
재구매 가능성이 높게 예측된 고객들을 대상으로 재구매 가능성이 높은 물품을 예측하는 모델 제작<br>

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
- Decision Tree
- LightGBM

→ 모델간의 correlation_heatmap <br>
<img src="https://user-images.githubusercontent.com/51469989/211970391-0bdee485-f745-4f5d-b10c-605b1435c9eb.png" width="50%"><br>

→ 가장 f1_score가 높은 LightGBM으로 테스트 진행 결정 <br>
- Test-Accuracy score <br>
<table>
  <tr>
    <th>auc</th>
    <th>accuracy</th>
    <th>f1</th>
    <th>precision</th>
  </tr>
  <tr>
    <td>약 0.82</td>
    <td>약 0.94</td>
    <td>약 0.14</td>
    <td>약 0.67</td>
  </tr>
</table>
<br>
소분류의 상품 수가 3519개로 많기 때문인지 auc와 accuracy가 높게 나왔지만, f1-score가 낮게 나온것은 아쉽게 느껴진다.
<br>

- Feature Importances
<img src="https://user-images.githubusercontent.com/51469989/211987596-30714c0b-cb81-4639-9929-386aad20ace4.png" width="50%"><br>
  
## A 군집
특성 : <br>
제안 : <br>
  
## B 군집
특성 : <br>
제안 : <br>
  
## C 군집

특성 : <br>
제안 : <br>

  
  
  
  
  
  
  
### 장바구니 분석(basket analysis = 연관 분석; Association Analysis;)
연관 규칙(association rule)은 비지도 학습¹의 하나로 대형 데이터베이스에서 변수간의 흥미로운 관계를 발견하기 위한 규칙기반 기계학습 방법이다. 연관 규칙 분석이란 대량의 정보로부터 개별 데이터 사이에서 연관규칙을 찾는 것인데, 예를 들어 마켓의 구매내역 중 특정 물건의 판매 발생 빈도를 기반으로 'A물건을 구매하는 사람들은 B물건을 함께 구매하는 경향이 있다' 라는 규칙을 찾을 수 있다. 따라서 다른말로 장바구니 분석이라고 불리기도 하는 것이다.

이러한 장바구니 분석은 POS 시스템이 나타나면서 세계적으로 퍼져나갔는데, 상품 구매 예측은 물론이고, 상품 추천이나 오프라인 상의 매대진열, 사은품 또는 패키지 상품 구성등 다방면으로 적용할 수 있다는 장점이 있어 마케팅 측면으로 많이 활용되는 분석이다.

연관규칙분석의 대표적인 알고리즘으로는 Apriori, FP-growth, DHP 등이 있는데 그 중 Apriori 알고리즘이 비교적 구현이 간단하고 높은 성능을 보여준다는 의견이 많아 프로젝트에 Apriori을 사용해보기로 했다.

#### Apriori 알고리즘
추천시스템의 1세대라고 할 수 있는 Apriori 알고리즘은 빈발항목집합²을 추출하는 것이 원리이다. 이해하기 쉬운 간단한 원리를 가지고 있으며 상품간의 많은 연관규칙을 발견할 수 있다는 장점이 있지만, 상품 수가 많을 수록 그 계산량이 기하급수적으로 늘어난다는 단점 역시 가지고 있다.
<br><br>
apriori 알고리즘을 사용법은 아래와 같다. 설정 지지도³(기본 값은 0.5, min_support 설정으로 값을 조절해줄 수 있다) 이상의 값을 가지는 연관 상품들을 얻어낼 수 있다.
```python
itemset = apriori(df, min_support=0.3, use_colnames=True)
itemset
```
이후 association_rules를 사용하여 설정 신뢰도⁴(기본 값은 0.8, min_threshold 설정으로 값을 조절해 줄 수 있다) 이상의 값을 가지는 목록을 확인할 수 있다.
```python
from mlxtend.frequent_patterns import association_rules
association_rules(itemset, metric="confidence", min_threshold=0.2)
```
association_rules 의 결과 값 중 lift(향상도)⁵라는 컬럼이 있는데, 이 수치가 클수록 우연히 일어나지 않았다는 표시이다. 아무런 관계가 없다면 1로 표현된다.


<sub>¹ 비지도 학습 : 목적변수(반응변수; 종속변수; 목표변수; 출력값)에 대한 정보 없이 학습이 이루어지는 학습</sub> <br>
<sub>² 빈발항목집합 : 최조지지도 이상을 가지는 항목집합. 모든 항목집합을 대상으로 했을 시 계산량이 복잡해질 수 있어, 최소지지도를 정한 후 그 이상의 값만 찾아 연관규칙을 생성한다. </sub> <br>
<sub>³ 지지도 : 전체 거래에서 특정물품 A와 B가 동시에 거래되는 비중. 해당 규칙이 얼마나 의미있는지 보여준다.<br> [A와 B가 동시에 일어난 횟수 / 전체 거래 횟수]</sub> <br>
<sub>⁴ 신뢰도 : A를 포함하는 거래 중 A와 B가 동시에 거래되는 비중.<br> [A와 B가 동시에 일어난 횟수/A가 일어난 횟수]</sub> <br>
<sub>⁵ 향상도 : A라는 상품에서 신뢰도가 동일한 상품 B와 C가 존재할 때, 어떤 상품을 더 추천해야 좋을지 판단. <br> [A와 B가 동시에 일어난 횟수 / A, B가 독립된 사건일 때 A, B가 동시에 일어날 확률]</sub> <br>

------


