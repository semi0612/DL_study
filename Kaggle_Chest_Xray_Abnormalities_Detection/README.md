# RSNA 2022 Cervical Spine Fracture Detection
- Colab, Jupyter lab에서 실험을 진행하였습니다
- 이 대회는 Vingroup Big Data Institute가 VinBigData의 웹 기반 플랫폼인 VinLab을 통해 수집한 이미지를 통해 기초 연구를 촉진하여 새롭고 적용 가능성이 높은 기술 조사를 목표로 한 대회이며, Kaggle Web Site를 통하여 데이터를 제공받았습니다.
- url : [https://www.kaggle.com/competitions/vinbigdata-chest-xray-abnormalities-detection/overview](https://www.kaggle.com/competitions/vinbigdata-chest-xray-abnormalities-detection/overview)

### 주제 선정 이유
이미지 데이터의 처리 및 분석에 대하여 궁금증이 있었기 때문에 프로젝트를 진행하며 공부가 되지 않을까 싶었고, 팀플을 함께 진행하는 팀원의 전공이 의료 관련이였기에 접목 시켜 분석 및 결과 해석이 용이 하지 않을까 싶어 의료 이미지를 이용해보는 것으로 방향을 잡았습니다.

### 주제(문제)
의사들은 CT와 PET스캔 MRI 그리고 X-ray와 같은 데이터를 이용하여 의학적 상태를 진단하고 치료하는데, 다른 부위의 X-ray결과보다 흉부 X-ray 결과는 의학적 오진이 생기기 쉬운 부위라고 한다. X-ray에서 작은 size의 이상까지 보다 정확하게 식별하고 위치를 파악하는 것은 정확한 진단을 내리는데 도움이 될 수 있을 것이다.

### 분석 목표
흉부의 X-ray 이미지만으로 이상 유무를 정확하게 평가할 수 있을까?<br>
⇒ 흉부 방사선 사진에서 13가지 유형의 흉부 이상과 1가지의 이상없음을 파악하고 bounding box 를 그려보는 것을 목표로 한다.

------

### 데이터 정보
