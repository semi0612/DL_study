위키북스의 '파이썬 머신러닝 완벅 가이드' 개정 2판으로 공부하고 있습니다. 실습 과정과 이해에 따라 내용의 누락 및 코드의 변형이 있을 수 있습니다.

----

# 감성 분석
감성 분석(Sentiment Analysis)은 문서의 주관적인 감정/의견/감성/판단 등을 주관적인 요소를 파악하기 위한 기법.

SNS, 여론조사, 온라인 리뷰, 피드백 등 다양한 분야에서 활용되고 있다. 여러가지 주관적인 단어와 문맥을 기반으로 감성 수치를 계산하는데 지도학습, 비지도 학습 모두가 가능하다

`지도학습`
-학습 데이터와 타깃 레이블 값을 기반으로 감성 분석 학습을 수행한 뒤 이를 기반으로 다른 데이터의 감성 분석을 예측하는 방법
-일반적으로 적용해온 학습/예측 과정으로 텍스트 기반의 분류와 거의 동일하다

`비지도 학습`
-감성분석을 위한 용어와 문맥에 대한 다양한 정보를 가지고 있는, 'Lexicon'라는 일종의 감성 어휘 사전을 이용해 문서의 긍정적, 부정적 감성 여부를 판단한다.

## 지도학습 기반 감성 분석 실습(IMDB 영화평)
[IMDB 영화 데이터](https://www.kaggle.com/c/word2vec-nlp-tutorial/data)를 실습 데이터로 사용할 것이다.

### 데이터 로드 및 구조
```python
# 데이터 로드
import pandas as pd

review_df = pd.read_csv('./labeledTrainData.tsv', header=0, sep='\t', quoting=3)
review_df[:3]
```
![](https://velog.velcdn.com/images/cyhse7/post/0f39e51a-794f-4202-ae12-cbdae340a0a3/image.png)

가져온 데이터의 구조를 살펴보면
- id : 각 데이터의 id
- sentiment : 영화평(review)의 결과값(Target Label). 1은 긍정적 평가 0은 부정적 평가
- review : 영화평 텍스트

### 텍스트 전처리

```python
review_df["review"][0]
```
```
'"With all this stuff going down at the moment with MJ i\'ve started listening to his music, watching the odd documentary here and there, watched T
...
이하 생략
```
하나만 뽑아서 확인해보니, 해당 데이터는 HTML형식에서 추출했기 때문에 `<br>` 태그가 존재하는 것을 확인할 수 있었다. 이는 피처로 만들 필요가 없기 때문에 삭제

또한 영어가 아닌 숫자/특수문자 역시 피처로는 별 의미가 없어 보이기 때문에 이를 정규표현식을 이용하여 공란으로 변경하려 한다.

```python
import re

# <br> html 태그는 replace 함수를 사용해 공백으로 변환
review_df['review'] = review_df['review'].str.replace('<br />', ' ')
review_df['review'] = review_df['review'].apply(lambda x : re.sub("[^a-zA-Z]", " ", x))
review_df['review']
```
```
0         With all this stuff going down at the moment ...
1           The Classic War of the Worlds   by Timothy ...
2         The film starts with a manager  Nicholas Bell...
3         It must be assumed that those who praised thi...
4         Superbly trashy and wondrously unpretentious ...
                               ...                        
24995     It seems like more consideration has gone int...
24996     I don t believe they made this film  Complete...
24997     Guy is a loser  Can t get girls  needs to bui...
24998     This    minute documentary Bu uel made in the...
24999     I saw this movie as a child and it broke my h...
Name: review, Length: 25000, dtype: object
```
이제 결정 값 클래스인 `sentiment` 칼럼을 별도로 추출해 결정 값 데이터 셋을 만들고, 원본 데이터 세트에서 id와 sentiment 칼럼을 삭제해 피처 데이터 셋을 생성 후 split해주자

```python
from sklearn.model_selection import train_test_split

class_df = y_target = review_df["sentiment"]
feature_df = X_feature = review_df.drop(['id', 'sentiment'], axis=1, inplace=False)

X_train, X_test, y_train, y_test= train_test_split(X_feature, y_target, test_size=0.3, random_state=156)

X_train.shape, X_test.shape
```
```
((17500, 1), (7500, 1))
```
학습용/테스트용 데이터가 175000/7500개로 나눠졌다. 

### 피처 벡터화 및 ML
이제 감상평 텍스트를 피처 벡터화한 후 ML 분류 알고리즘을 적용해 예측 성능을 측정하려 한다. Pipeline객체를 이용해 두 가지를 한번에 수행하려 한다.

```python
from sklearn.feature_extraction.text import CountVectorizer, TfidfVectorizer
from sklearn.pipeline import Pipeline
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score, roc_auc_score

# 피처 벡터화: CountVectorizer, ML: LogisticRegression
pipeline = Pipeline([("cnt_vect", CountVectorizer(stop_words="english", ngram_range=(1,2) ) ),
                     ("LR", LogisticRegression(solver='liblinear', C=10) )
                    ])

# 학습
pipeline.fit(X_train['review'], y_train)
# 예측
pred = pipeline.predict(X_test['review'])
pred_probs = pipeline.predict_proba(X_test['review'])[:,1]
# 평가
acc = accuracy_score(y_test, pred)
auc = roc_auc_score(y_test, pred_probs)

print(f"예측 정확도: {acc:.4f}, ROC-AUC: {auc:.4f}")
```
```
예측 정확도: 0.8859, ROC-AUC: 0.9503
```
꽤 높게 나왔다.

피처 벡터화는 카운트 벡터화를 적용, ML은 로지스틱을 이용하였다.

이번에는 TF-IDF 벡터화를 적용한 뒤 다시 예측 성능을 측정해보려한다.
```python
# 피처 벡터화: TfidfVectorizer, ML: LogisticRegression
pipeline = Pipeline([("tfidf_vect", TfidfVectorizer(stop_words="english", ngram_range=(1,2) ) ),
                     ("LR", LogisticRegression(C=10) )
                    ])

# 학습
pipeline.fit(X_train['review'], y_train)
# 예측
pred = pipeline.predict(X_test['review'])
pred_probs = pipeline.predict_proba(X_test['review'])[:,1]
# 평가
acc = accuracy_score(y_test, pred)
auc = roc_auc_score(y_test, pred_probs)

print(f"예측 정확도: {acc:.4f}, ROC-AUC: {auc:.4f}")
```
```
예측 정확도: 0.8936, ROC-AUC: 0.9598
```
Count기반 피처 벡터화의 예측 정확도는 `0.8859`
TF-IDF기반 피처 벡터화의 예측 정확도는 `0.8936`
으로 조금이지만 피처 벡터화의 예측 성능이 조금 더 좋게 나왔다.

#### read_csv 옵션 - `\t`
위에서 사용한 `labeledTrainData.tsv`는 탭(`\t`) 문자로 분리된 파일인데, 판다스의 `read_csv()` 의 인자로 sep='`\t`' 을 명시해주면 무리없이 읽어올 수 있다.

#### read_csv 옵션 - quoting
값을 읽거나 쓸 때 둘러쌀 문자 컨벤션
<table>
  <tr style=" backgroundColor:lightgray">
    <th style="width:100px;">값</th>
    <th>설명</th>
  </tr>
  <tr>
    <td>0</td>
    <td>QUOTE_MINIMAL<br>기본값<br>최소한의 데이터만 묶겠다.<br>(예를 들어 쉽표가 포함된 데이터만 묶음) </td>
  </tr>
  <tr>
    <td>1</td>
    <td>QUOTE_ALL<br>모든 데이터를 자료형에 상관없이 묶겠다.<br>모든 데이터를 문자열형으로 처리</td>
  </tr>
  <tr>
    <td>2</td>
    <td>QUOTE_NONNUMERIC<br>숫자 데이터가 아닌 경우에만 묶는다.<br> 데이터를 읽어올 때 묶이지 않은 데이터는 csv 객체에 의해 실수형으로 읽어오게 됨</td>
  </tr>
  <tr>
    <td>3</td>
    <td>QUOTE_NONE<br>데이터를 묶는 작업 하지 않음<br>큰 따옴표를 무시한다</td>
  </tr>
</table>
